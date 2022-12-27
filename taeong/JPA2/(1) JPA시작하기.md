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
