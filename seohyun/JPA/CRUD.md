### JPA 에서의 CRUD

# Create
```
Member member = new Member();
member.setId(2L);
member.setName("HelloB");
```
# Read
```
Member findMember = em.find(Member.class, 1L);
System.out.println("findMember.id = " + findMember.getId());
System.out.println("findMember.name = " + findMember.getName());
```
# Update
```
findMember.setName("HelloJPA");

// em.persist(findMember); // -> 부분이 필요 없다. 자바의 Collection 을 다루는 것과 동일하므로
```
# Delete
```
em.remove(findMember);
```

# 주의할 점
- **엔티티 매니저 팩토리**는 하나만 생성해서, 애플리케이션 전체에서 공유한다.
```
EntityManagerFactory emf =Persistence.createEntityManagerFactory("hello");

EntityManager em = emf.createEntityManager();

EntityTransaction tx = em.getTransaction();
tx.begin();

try {
    Member member = new Member();
    member.setId(2L);
    member.setName("HelloB");

    em.persist(member);
    tx.commit();
} catch (Exception e) {
    tx.rollback();
} finally {
    em.close();
}

em.close();

emf.close();
```
- **엔티티 매니저**는 쓰레드간에 공유하지 않는다. 사용하고 버려야 함
- **JPA의 모든 데이터 변경은 트랜잭션 안에서 실행**
