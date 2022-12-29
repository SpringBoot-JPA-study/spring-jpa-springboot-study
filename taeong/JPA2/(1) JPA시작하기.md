## Hello JPA - 프로젝트 생성

- jpa는 특정 데이터베이스에 종속적이지 않도록 설계되어있음
- 각각의 데이터베이스가 제공하는 SQL 문법과 함수는 조금씩 다름
    - ex. 가변 문자 : MySQL은 VARCHAR, Oracle은 VARCHAR2처럼
    - 방언 : SQL 표준을 지키지 않는 특정 데이터베이스만의 고유한 기능


<BR>
  
## JPA 구동방식

- JPA에는 Persistence라는 클래스가 있음
    - /META-INF/persistence.xml 에 있는 설정 정보를 읽어옴
- EntityManagerFactory라는 클래스를 만듦
    - 여기서 필요할 때마다(DB커넥션을 얻어서 쿼리를 날리고 종료할 때마다) EntityManager를 만들어냄
    
    ```java
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("persistent.xml에 등록된 name");
    EntityManager em = em.createEntityManager();
    
    /* 트랜잭션 */
    
    em.close();
    emf.close();
    ```
    
- **JPA에서 데이터를 변경하는 모든 작업은 트랜잭션에서 이루어져야함**
    
    ```java
    EntityTransaction tx = em.getTransaction();
    tx.begin();
    
    /* 데이터 변경 코드 */
    
    tx.commit();
    ```
    
- 데이터 변경
    
    ```java
    Member member = new Member();
    
    member.setId(1L);
    member.setName("태영");
    
    em.persist(member);
    
    ```
    
- try-catch로 예외처리(정석코드)
    
    ```java
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("persistent.xml에 등록된 name");
    EntityManager em = em.createEntityManager();
    
    EntityTransaction tx = em.getTransaction();
    tx.begin();
    
    try{
    		/* 데이터 변경 코드 */
    		tx.commit();
    }catch(Exception e){
    		tx.rollback();
    }finally{
    		em.close();
    }
    emf.close();
    ```
    
    - 이제는 이렇게 구현할 필요가 없음. 스프링이 자동으로 해주니까!
    

<br>
    
### 데이터를 수정하는 경우

- 멤버의 이름을 수정하는 경우
    
    ```java
    findMember.setName("서현");
    ```
    
    - `em.persist(findMember)`를 작성하지 않아도 됨!
- 이유
    - JPA를 통해서 엔티티를 가져오면 JPA가 관리를 하게 됨
    - 트랜잭션 커밋 시점에 변경 여부를 체크함
    - 뭔가 변경된 부분이 있으면 자동으로 업데이트 쿼리를 만들어 날림

<br>
    
    
### 주의

- 엔티티 매니저 팩토리는 웹 서버가 올라오는 시점에 딱 한 번만 생성됨
- 엔티티 매니저는 요청이 들어올 때마다 생성했다가 다 쓰면 버림
- 엔티티 매니저는 스레드간에 공유하면 절대 안됨! → 장애의 주범
- JPA의 모든 데이터 변경은 트랜잭션 안에서 실행되어야 함

<br>
    
### JPQL

1. 등장배경
    - JPA를 사용하면 엔티티 객체를 중심으로 개발
    - 그러나 검색을 할 때 JPA는 엔티티 객체를 대상으로 검색하지만 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능
    - 애플리케이션이 필요한 데이터를 불러오는 경우 검색 조건이 포함된 SQL이 필요할 수 밖에 없음
    - 이 때 사용하는 것이 JPQL!
2. JPQL
- JPQL : SQL을 추상화한 객체 지향 쿼리 언어
- 테이블이 아니라 Member 객체를 대상으로 쿼리를 작성하는 개념!
- ex. DB에서 멤버 목록을 조회하는 경우
    
    ```java
    List<Member> memberList = em.createQuery("select m from Member", Member.class).getResultList();
    ```
    
- ex. 페이징을 하는 경우
    
    ```java
    List<Member> memberList = em.createQuery("select m from Member", Member.class)
    				.setFirstResult(5)
    				.setMaxResults(8)
    				.getResultList();
    ```
