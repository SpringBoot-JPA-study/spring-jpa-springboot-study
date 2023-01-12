## MemberRepository 인터페이스가 동작하는 이유?

- 스프링 데이터 JPA가 인터페이스를 보고 구현체를 만들어서 주입해준다.
  - 프록시 형태로 생성해서 주입한다. → `class jdk.proxy2.$Proxy117`
- `개발자가 인터페이스만 만들어놓으면, 구현체는 스프링 데이터 JPA가 알아서 만들어준다.`
- `@Repository` 어노테이션 생략이 가능하다.
  - 컴포넌트 스캔을 스프링 데이터 JPA가 자동으로 처리하기 때문에 생략 가능

---

## 공통 인터페이스 분석 (JpaRepository)

- JpaRepository 인터페이스는 `공통 CRUD 기능`을 제공한다.
- 제네릭은 **`<엔티티 타입, 식별자>`** 로 작성한다.

**주요 메서드**

- `save(T)` : 새로운 엔티티는 `저장(persist)`하고, 이미 있는 엔티티는 `병합(merge)`한다.
- `delete(T)` : 엔티티를 삭제한다. 내부에서 `em.remove()` 호출
- `findAll(...)` : 모든 엔티티를 조회한다. 정렬, 페이징 조건을 파라미터로 제공할 수 있다.
- `findById(ID)` : 엔티티 하나를 조회한다. 내부에서 `em.find()` 호출
- `getOne(ID)` : 엔티티를 프록시로 조회한다. 내부에서 `em.getReference()` 호출
- `count()` : 엔티티의 개수를 조회한다. int가 아닌 `long 타입을 반환`한다.

> `JpaRepository`는 대부분의 공통 메서드를 제공한다.

---

## 쿼리 메서드 (Query Method)

- 메서드 이름으로 쿼리를 생성하고 실행하는 기능이다.
- 정해진 메서드 이름 형식을 사용해서 특정 조건을 실행하는 쿼리문을 생성할 수 있다.
  **→ [쿼리 메서드 필터 조건](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation)**

`MemberJpaRepository.java`

```java
 public List<Member> findByUsernameAndAgeGreaterThan(String username, int age) {
    return em.createQuery("select m from Member m where m.username = :username" +
            " and m.age > :age", Member.class)
            .setParameter("username", username)
            .setParameter("age", age)
            .getResultList();
}
```

- 순수 JPA만을 사용하는 경우 직접 JPQL로 쿼리를 작성해야 한다.

`MemberRepository.java`

```java
List<Member> findByUsernameAndAgeGreaterThan(String username, int age);
```

- 쿼리 메서드 기능으로 메서드 이름만으로 쿼리문을 생성할 수 있다.
- `파라미터는 메서드 이름에 먼저 나온 필드부터 순서대로 작성해야한다.`

<br>

- **장점**

  - 메서드 이름만으로 간단하게 쿼리문을 생성할 수 있다.
  - 애플리케이션 로딩 시점에 오류를 발견할 수 있다.

- **단점**
  - 조건 필드의 개수가 많아지면 메서드의 이름이 너무 길어진다.
    - `@Query` 어노테이션으로 해결 가능하다.
  - 엔티티 필드명이 변경되면 메서드 이름도 함께 변경해야 한다.

---

## @Query 기능

- 메서드에 정적 쿼리를 직접 작성하는 기능 → `이름 없는 Named 쿼리`라고 할 수 있다.
- 복잡한 쿼리문을 작성할 때 매우 강력한 기능이다.

**`쿼리 메서드`**

```java
List<Member> findByUsernameAndAgeGreaterThan(String username, int age);
```

- 쿼리 메서드는 간단하게 쿼리문을 만들 수 있지만 메서드 이름이 너무 길다..
  - 필드가 2개인데도 이 정도면 3개 이상은 상상도 하기 싫다..

**`@Query 어노테이션`**

```java
@Query("select m from Member m where m.username = :username and m.age = :age")
List<Member> findMember(@Param("username") String username, @Param("age") int age);
```

- 쿼리문을 따로 분리해서 작성하는 방식이기 때문에, 메서드의 이름을 간단하게 작성할 수 있다.
- `@Param` 어노테이션으로 파라미터 바인딩을 반드시 해줘야한다.
- 실무에서 자주 사용하는 기능이다.

<br>

- **장점**
  - 메서드의 이름을 간단하게 작성할 수 있다.
  - 복잡한 쿼리문을 작성이 간편하다.
  - **`애플리케이션 실행 시점에 문법 오류 발견이 가능하다.`**

### @Query로 값, DTO 조회하기

```java
@Query("select m.username from Member m")
List<String> findUsernameList();
```

```java
@Query("select new study.datajpa.member.entity.MemberDto(m.id, m.username, t.name) from Member m join m.team t")
List<MemberDto> findMemberDto();
```

- DTO 조회시에는 DTO의 전체 경로를 작성해야한다.
  - **`Query dsl`** 로 개선 가능!

> **간단한 쿼리문은 `쿼리 메서드`, 복잡한 쿼리문은 `@Query`를 사용해서 작성하자!**
