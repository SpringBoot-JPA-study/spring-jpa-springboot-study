# JPQL - fetch join 기본

**실무에서 정말정말 중요함!!** ⭐⭐⭐

<br>

## 페치 조인이란?

- SQL 조인 종류가 아니라 JPQL의 전용 기능
- JPQL에서 성능 최적화를 위해 제공함
- 연관된 엔티티나 컬렉션을 한 방 쿼리로 조회 가능함
    - 쿼리 두 번 나갈 것 같아도 한 번의 쿼리로 해결할 수 있기 때문에 효과가 큼
- `join fetch` 명령어를 사용함

<br>

## 엔티티 페치 조인

- `페치 조인 ::= [ LEFT [OUTER] | INNER ] JOIN FETCH 조인경로`
- EX) 회원을 조회하면서 연관된 팀도 함께 조회하고 싶은 경우
    - JPQL
        
        ```java
        select m from Member m join fetch m.team
        ```
        
        - m만 프로젝션
        - 조인과 유사한 형
    - SQL
        
        ```sql
        SELECT M.*, T.* FROM MEMBER M INNER JOIN TEAM T ON M.TEAM_ID = T.ID
        ```
        
        - JPQL에서 m만 프로젝션했음에도 불구하고 팀의 데이터도 조회하는 쿼리가 실행됨
    - 실행 결과
        
        ![https://user-images.githubusercontent.com/48792230/222747252-ca76503a-89d6-49c4-a9e3-bd2632100967.png](https://user-images.githubusercontent.com/48792230/222747252-ca76503a-89d6-49c4-a9e3-bd2632100967.png)
        

<br>

## N:1 - 페치 조인을 사용하지 않는다면? (N+1문제 발생)

위의 테이블에서 아래와 같이 페치 조인을 사용하지 않은 JPQL을 작성하고 출력하는 경우,

```java
String jpql = "select m from Member m";
List<Member> members = em.createQuery(jpql, Member.class).getResultList();

for(Member member : members){
	System.out.println(member.getUsername());
	System.out.println(member.getTeam().name());
}
```

1. JPQL대로 먼저 Member테이블 정보를 데이터베이스에서 조회 (`SQL생성`)
2. 회원1의 team.name은 현재 프록시 객체이므로 team.id=1인 데이터를 조회 (`SQL생성`)
3. 회원2의 team.id=1이므로 team.name은 회원1와 동일
4. 회원3의 team.name은 현재 프록시 객체이므로 team.id=2인 데이터를 조회 (`SQL생성`)

⇒ 총 3번의 쿼리 발생 (최악의 경우에는 무수히 많은 쿼리 발생 가능성 있음)

⇒ **페치 조인을 하는 경우 Team 객체는 프록시가 아닌 실제 엔티티이기 때문에 N+1문제 발생X**

- 지연로딩으로 설정해도 페치 조인이 우선이기 때문에 지연 로딩 적용X

<br>

## 1:N - 컬렉션 페치 조인

- 일대다 관계에서 컬렉션 페치 조인을 사용하는 경우
- JPQL
    
    ```sql
    select t from Team t join fetch t.members where t.name = '팀A'
    ```
    
- SQL
    
    ```sql
    SELECT T.*, M.*
    FROM TEAM T INNER JOIN MEMBER M ON T.ID = M.TEAM_ID
    WHERE T.NAME = '팀A'
    ```
    
- 실행 결과
    
    ![https://user-images.githubusercontent.com/48792230/222747266-1fc5b704-c438-4792-923d-5d21dd1fe6c7.png](https://user-images.githubusercontent.com/48792230/222747266-1fc5b704-c438-4792-923d-5d21dd1fe6c7.png)
    
    - team.id와 team.name의 값은 중복이 됨
    - 컬렉션 안에는 같은 주소값을 가진 객체가 중복으로 들어감
        - JPA는 데이터베이스에서 반환한 값(중복이 있더라도) 그대로 컬렉션에 저장
    - **일대다 컬렉션 페치 조인은 데이터의 수가 예상보다 많이 출력됨**
        - 예상은 TEAM의 데이터 수와 TEAM별 members 컬렉션의 크기가 같아야 함

<br>

## 컬렉션 페치 조인 중복 문제 해결방법 : **DISTINCT**

- **DISTINCT : 중복 결과 제거**
    - SQL의 DISTINCT만으로는 중복을 다 제거할 수 없음
        - SQL의 DISTINCT는 조인된 결과에서 인스턴스가 완전히 같아야 중복으로 인식함
        - 위의 이미지처럼 조인된 결과에서 member.id와 member.name이 다르기 때문에 제거되지 않음
    - **JPQL의 DISTINCT 기능 2가지**
        1. `SQL에 DISTINCT를 추가`
        2. `애플리케이션 레벨에서 데이터베이스에서 반환된 엔티티들의 중복을 제거함`
            - 같은 식별자를 가진 Team 엔티티를 제거함
        

<br>

## 페치 조인과 일반 조인의 차이 ⭐

- 일반 조인시 연관 엔티티를 함께 조인하지 않음
    
    ```sql
    select t from Team t join (fetch) t.members m
    ```
    
    - 일반 조인 : SQL에서는 join절이 출력되지만 실제로 Team데이터만 가져오고 members에 대한 데이터는 프록시 상태
    - 페치 조인 : SQL에서는 join절이 출력되고 Team, members 모두 실제 데이터 반환
- JPQL은 결과를 반환할 때 연관관계를 고려하지 않고, 오직 select절에 지정한 엔티티만 조회함
- 페치 조인을 사용할 때는 연관 엔티티까지 함께 조회 (=즉시 로딩)
- **페치 조인은 객체 그래프를 SQL 한 방 쿼리로 조회하는 개념**
