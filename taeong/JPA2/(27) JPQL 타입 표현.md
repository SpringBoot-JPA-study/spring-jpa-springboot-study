# JPQL 타입 표현

### **ENUM**

- 패키지명까지 포함해서 작성해야 함
- ex) jpabook.MemberType.Admin
    - `패키지명` : jpabook
    - `ENUM 클래스명` : MemberType
    - `ENUM 타입` : Admin
- **주의점** : @Enumerated는 기본이 항상 ordinal이기 때문에 String으로 변경해야함
- 다른 방법 : **파라미터 바인딩**
    
    ```sql
    
    String query = "select m.username from Member m where m.type =:type";
    List<Object[]> result = em.createQuery(query)
    		.setParameter("userType", MemberType.ADMIN)
    		.getResultList();
    ```
    
    - 대부분 이렇게 사용함

<br>

### 엔티티 타입

- 상속관계에서 엔티티의 타입 정보가 Member인 경우만 출력하고 싶은 경우
- TYPE(m) = Member
    
    ```sql
    em.createQuery("select i from Item i where type(i) = Book", Item.class);
    ```
    
- @DiscriminatorValue로 엔티티 타입명을 변경할 수 있음
    - JPQL 쿼리를 변경한 엔티티 타입명으로 작성할 필요는 없음. 
    그대로 클래스명을 작성하면 자동으로 SQL 쿼리로 번역될 때 변경 엔티티 타입명으로 전달됨
