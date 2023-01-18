## 페이징

### 순수 JPA 페이징

`MemberJpaRepository.java`

```java
public List<Member> findListByPaging(int age, int offset, int limit) {
    return em.createQuery("select m from Member m where m.age = :age order by m.username desc", Member.class)
            .setParameter("age", age)
            .setFirstResult(offset)
            .setMaxResults(limit)
            .getResultList();
}

public long count(int age) {
    return em.createQuery("select count(m) from Member m where m.age = :age", Long.class)
            .setParameter("age", age)
            .getSingleResult();
}
```

- 특정 나이를 기준으로 페이징하는 방식이다.
- `count 쿼리는 전체 개수를 조회해오기 때문에 정렬 조건이 필요없다.`
- count 메서드를 long 타입으로 반환한 이유는 스프링 데이터 JPA에서 count 메서드의 반환 타입이 long 타입이기 때문이다.

---

### 스프링 데이터 JPA

**`Page 객체 반환`**

```java
import org.springframework.data.domain.Page;

Page<Member> findByAgeGreaterThanEqual(int age, Pageable pageable);
```

**✨ `Page Test`**

```java
void paging() throws Exception {
    for (int i = 1; i <= 5; i++) {
        memberRepository.save(new Member("member" + i, i * 10));
    }

    PageRequest pageRequest = PageRequest.of(0, 3, Sort.by(Direction.DESC, "username"));

    Page<Member> page = memberRepository.findByAgeGreaterThanEqual(30, pageRequest);
}
```

- `Page` 객체를 반환받고, 파라미터로 `Pageable` 인터페이스를 작성한다.
- Pageable의 구현체인 `PageRequest` 객체를 만들어서 작성한다.
  - **`Page는 1이 아닌 0부터 시작한다!`**
  - 정렬 조건이 필요한 경우 `Sort.by` 로 정렬 조건을 추가한다.
- Page 객체는 spring data에서 제공하는 객체이다.
  - 데이터베이스 기술이 변경되도 영향이 없다.

**`결과 쿼리`**

```sql
 select
        member0_.id as id1_0_,
        member0_.age as age2_0_,
        member0_.team_id as team_id4_0_,
        member0_.username as username3_0_
    from
        member member0_
    where
        member0_.age>=30
    order by
        member0_.username desc limit 3

select
        count(member0_.id) as col_0_0_
    from
        member member0_
    where
        member0_.age>=30
```

- `Page를 반환`하는 경우 count 쿼리를 함께 실행한다.
- 단, 쿼리가 복잡한 경우에는 count 쿼리를 분리해서 작성하는 게 성능상 더 좋다.

**`Slice 객체 반환`**

```java
import org.springframework.data.domain.Slice;

Slice<Member> findByAgeGreaterThanEqual(int age, Pageable pageable);
```

- `Slice를 반환`하는 경우에는 count 쿼리를 실행하지 않는다.
- 지정한 limit에 1을 더해서 조회한다. → **`limit가 3이면 4로 조회`**

**`결과 쿼리`**

```sql
 select
        member0_.id as id1_0_,
        member0_.age as age2_0_,
        member0_.team_id as team_id4_0_,
        member0_.username as username3_0_
    from
        member member0_
    where
        member0_.age>=30
    order by
        member0_.username desc limit 4
```

### ✨ count 쿼리 분리하기

```java
@Query("select m from Member m left join m.team t")
Page<Member> findBy(Pageable pageable);
```

```sql
 select
        count(member0_.id) as col_0_0_
    from
        member member0_
    left outer join
        team team1_
            on member0_.team_id=team1_.id
```

- 조인이 들어간 복잡한 쿼리의 경우 count 쿼리에도 조인이 들어가기 때문에 이런 경우에는 분리해서 사용하는 것이 좋다.

**`✨ count 쿼리 분리`**

```java
@Query(value = "select m from Member m left join m.team t",
       countQuery = "select count(m) from Member m")
Page<Member> findBy(Pageable pageable);
```

```sql
select
        member0_.id as id1_0_,
        member0_.age as age2_0_,
        member0_.team_id as team_id4_0_,
        member0_.username as username3_0_
    from
        member member0_
    left outer join
        team team1_
            on member0_.team_id=team1_.id
    order by
        member0_.username desc limit ?
```

```sql
select
    count(member0_.id) as col_0_0_
from
    member member0_
```

- **`조회 쿼리에는 조인이 들어가고, count 쿼리에는 조인이 빠진 걸 확인할 수 있다.`**

### DTO로 변환해서 반환하기

- 엔티티를 직접 반환하면 API의 스펙이 바뀔 수 있는 위험이 있다.
  - 반드시 DTO로 변환해서 반환하는 것이 좋다.

```java
Page<Member> page = memberRepository.findByAgeGreaterThanEqual(30, pageRequest);

Page<MemberDto> map = page.map(m -> new MemberDto(m));
```
