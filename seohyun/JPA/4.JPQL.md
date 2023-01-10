### JPQL 소개
- JPA를 사용하면, 엔티티 객체를 중심으로 개발한다.
- 검색 쿼리인 경우에는? 어떻게 해야하는지
- 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능하다.
- 애플리케이션이 필요한 데이터만 DB에서 불러오려면, 결국 검색 조건이 포함된 SQL이 필요하다.
- JPA는 SQL을 추상화한 JPQL이라는 객체 지향 쿼리 언어를 제공한다.
- JPQL은 SQL과 문법이 유사하다. ex) SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN 지원
- **JPQL은 엔티티 객체**를 대상으로 쿼리하는 반면, **SQL은 데이터베이스 테이블**을 대상으로 쿼리한다.
- 즉, JPQL은 테이블이 아닌, **객체를 대상으로 검색하는 객체 지향 쿼리**
- SQL을 추상화해서, 특정 데이터베이스 SQL에 의존 X
- JPQL을 한마디로 정의하면, **객체 지향 SQL**
- 가장 단순한 조회 방법
  - EntityManager.find()
  - 객체 그래프 탐색(a.getB().getC())
  ```
    List<Member> result = em.createQuery("select m from Member as m", Member.class)
                            .getResultList();
  ```
  <img width="447" alt="image" src="https://user-images.githubusercontent.com/62336151/210814487-fc5aec94-fa83-4415-af69-cbafdebbac7d.png">
