# Named 쿼리

- 미리 JPQL을 정의해서 이름을 붙여두고 사용하는 쿼리
    - `Member.findByUsername` 라는 이름의 JPQL을 사용하는 경우
    
    <br>
    
    ```java
    @Entity
    @NamedQuery(
    	name = "Member.findByUsername",
    	query = "select m from Member m where m.username = :username")
    public class Member{
    	...
    }
    ```
    
    ```java
    List<Member> result = em.createNamedQuery("Member.findByUsername", Member.class)
    	.setParameter("username","회원1")
    	.getResulstList();
    ```
    
    - 이름으로 쿼리를 불러와서 사용할 수 있음

<br>

## 특징

- 정적쿼리만 가능

<br>

- 어노테이션, XML에 정의
    - XML이 항상 우선권을 가짐
    - 애플리케이션 운영 환경에 따라 다른 XML을 배포할 수 있음
    - 순수 JPA에서 어노테이션에 정의한 Named 쿼리는 양이 늘어날 수록 해당 엔티티가 너무 지저분해진다는 단점이 있음
    - Spring data JPA를 사용하면 인터페이스 메소드 위에 어노테이션으로 바로 선언할 수 있음 (이름 없는 Named 쿼리)
        - 인터페이스 파일에 정의하기 때문에 엔티티 파일의 가독성 문제를 해결할 수 있음

<br>

- **애플리케이션 로딩 시점에 초기화 후 재사용** ⭐⭐
    - 동적 쿼리로 변경될 가능성이 없기 때문에
    - JPA나 하이버네이트가 해당 쿼리를 SQL로 파싱해서 캐싱해두고 있음
    - 애플리케이션 로딩 시점에 캐싱해두기 때문에 JPQL이 SQL로 파싱되는 비용이 거의 없음 → 성능상 이점

<br>

- **애플리케이션 로딩 시점에 쿼리를 검증** ⭐⭐⭐
    - 개발할 때 가장 좋은 오류는 컴파일타임에 나는 오류
    - 런타임 중에서는 로딩시점에 나는 오류 (대부분 다 잡을 수 있기 때문)
