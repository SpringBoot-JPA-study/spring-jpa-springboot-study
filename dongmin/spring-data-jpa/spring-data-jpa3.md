## 파라미터 바인딩

- `이름 기반`, `위치 기반` 2가지의 방법을 제공한다.
- 위치 기반 바인딩은 위치를 기준으로 바인딩 되기 때문에, 중간에 파라미터가 추가되면 엉뚱한 값이 바인딩 되는 경우가 많다.
- **`이름 기반 파라미터 바인딩을 사용하자!`**

✨ **`이름 기반`**

```java
select m from Member m where m.username = :name
```

❌ `위치 기반`

```java
select m from Member m where m.username = ?0
```

### 컬렉션 파라미터 바인딩

- 스프링 데이터 JPA에서 컬렉션은 in절에 바인딩이 가능하다.

```java
@Query("select m from Member m where m.username in :names")
List<Member> findByNames(@Param("names") List<String> names);
```

`MemberRepositoryTest.java`

```java
@Test
void collectionBindingTest() throws Exception {
    Member member1 = new Member("김영한");
    Member member2 = new Member("박은빈");
    Member member3 = new Member("유재석");
    memberRepository.save(member1);
    memberRepository.save(member2);
    memberRepository.save(member3);

    List<Member> members = memberRepository.findByNames(Arrays.asList("박은빈", "유재석", "김영한"));
    assertThat(members.size()).isEqualTo(3);
}
```

`결과 쿼리문`

```sql
select ...
from member member0_
where member0_.username in ('박은빈' , '유재석' , '김영한');

```

- in 절에 리스트 안에 들어있던 이름들이 들어간 걸 확인할 수 있다.

## 반환 타입

- 스프링 데이터 JPA는 유연한 반환 타입을 지원한다.

```java
List<Member> findListByUsername(String name);

Member findMemberByUsername(String name);

Optional<Member> findOptionalByUsername(String name);
```

- 컬렉션은 결과가 없으면 빈 컬렉션을 반환한다. → **`null 아님`**
- 단건 조회
  - 결과 없음 : null 반환
    - 내부에서 `getSingleResult()` 메서드 호출
    - 순수 JPA의 경우에는 `javax.persistence.NoResultException` 예외가 발생한다.
    - 스프링 데이터 JPA는 해당 예외를 무시하고 null을 반환한다. (좋다..)
  - 결과가 2건 이상 : `javax.persistence.NonUniqueResultException` 예외가 발생한다.
    - `org.springframework.dao.IncorrectResultSizeDataAccessException` 예외로 변환해서 반환한다. → `JPA 예외를 스프링 예외로 변환`
    - JPA에서 다른 DB 기술로 변경되어도 영향이 없도록 하기 위해서 이런 기능을 제공한다.
      - 몽고 DB, JDBC 등등..
