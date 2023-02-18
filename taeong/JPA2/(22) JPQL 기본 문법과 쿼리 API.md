# JPQL 기본 문법과 쿼리 API

- JPQL은 객체지향 쿼리 언어
    - 테이블을 대상으로 하는 것이 아니라 엔티티 객체를 대상으로 쿼리함
- SQL을 추상화함
    - 특정 이터베이스 SQL에 의존하지 않음
- 그치만 결과적으로는 SQL로 변환됨

<br>

## JPQL 문법

```java
select m from Member as m where m.age > 18
```

- 엔티티와 속성은 대소문자 구분 O, JPQL은 SQL와 같이 대소문자 구분X
- 테이블명이 아니라 엔티티명을 사용함
- 별칭(m)은 필수, as은 생략 가능
- count, avg, sum, max, min, group by, having, order by 다 사용 가능

<br>

## TypeQuery

| TypeQuery | Query |
| --- | --- |
| 반환 타입이 명확한 경우 사용 | 반환 타입이 명확하지 않을 때 사용 |

- TypeQuery 코드 예시
    - ex1) Member 객체를 반환하는 경우
    
    ```java
    TypeQuery<Member> query = em.createQuery("select m from Member m", Member.class);
    ```
    
    - ex2) 문자열을 반환하는 경우
    
    ```java
    TypeQuery<String> query = em.createQuery("select m.username from Member m", String.class);
    ```
    
- Query 코드 예시
    - ex) Member 객체의 속성을 복수 개 반환하는 경우
    
    ```java
    Query query = em.createQuery("select m.username, m.age from Member m");
    ```
    
<br>


## 결과 조회 API

- 결과가 하나 이상일 때 : `getResultList()`
    - 결과가 없으면 빈 리스트 반환
- 결과가 정확히 하나인 경우 : `getSingleResult()`
    - 결과가 없으면 NoResultException 반환
    - 두 개 이상이면 NonUniqueResultException 반환
    - JPA 표준 스펙에서는 두 경우 모두 예외를 터뜨림 → 조심해서 사용해야 함
        - Spring Data JPA의 경우 결과가 없으면 NULL (또는 Optional) 반환, 예외를 터뜨리지 않도록 알아서 잘 짜져있음
        

<br>

## 파라미터 바인딩 (이름 기준)

```java
Member result = em.createQuery("select m from Member m where m.username = :username", Member.class)
		.setParameter("username", "taeong")
		.getSingleResult();
```

- `= :파라미터명`
- `setParameter(”속성명”, “속성값”);`
