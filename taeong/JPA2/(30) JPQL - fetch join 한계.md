# JPQL - fetch join 한계

## 페치 조인을 사용할 때는 별칭을 사용하지 않는다.

- 페치 조인을 사용하는 이유 : 연관된 모든 엔티티를 다 가져와서 사용하기 위함
- 페치 조인 코드
    1. 별칭을 사용하지 않는 코드 예시
        
        ```java
        select t from Team t join fetch t.members
        ```
        
        → members의 모든 데이터를 가져오기 때문에 객체 그래프를 통해 모든 데이터를 조회할 수 있음
        
    2. 별칭을 사용한 코드 예시
        
        ```java
        select t from Team t join fetch t.members m where m.age > 10
        ```
        
        → members 중에 where절 조건에 해당하는 데이터만 가져오기 때문에 객체 그래프로 모든 데이터를 탐색할 수 없음
        
- 페치 조인에서 별칭을 사용하게 되면 JPA의 의도와 달라진다!
    - JPA에서 의도한 페치 조인은 객체 그래프를 통해 모든 데이터를 조회할 수 있어야 함
    - 별칭을 사용하면 모든 데이터를 탐색할 수 없음
    - 설계 의도와 달라지기 때문에 제대로 동작하지 않을 수 있음
    - ⇒ 페치 조인에서 별칭을 지원하긴 하지만 사용하지 말자!

<br>

## 둘 이상의 컬렉션은 페치 조인할 수 없다.

- 만약 Team의 members라는 컬렉션과 orders라는 컬렉션이 있다고 할 때, 두 컬렉션을 함께 조회하기 위해 아래와 같은 쿼리를 출력하는 JPQL을 작성했다고 가정
- SQL
    
    ```sql
    SELECT T.*, M.*, O.*
    FROM TEAM T 
    INNER JOIN MEMBER M ON T.ID = M.TEAM_ID
    INNER JOIN ORDERS O ON T.ID = O.TEAM_ID
    WHERE T.NAME = '팀A'
    ```
    
- 1 : N : M 이므로 1 * N * M 개의 데이터로 뻥튀기 될 위험있음
- 그렇기 때문에 컬렉션은 딱 하나만 지정할 수 있음

<br>

## 컬렉션을 페치조인하면 페이징 API를 사용할 수 없다.

- 일대일, 다대일은 페이징 API 사용 가능
- 일대다는 데이터 뻥튀기의 위험이  있음
    - 아래의 JPQL을 실행하면 다음과 같이 출력됨
    
    ```sql
    select t from Team t join fetch t.members where t.name = '팀A'
    ```
    
    ![https://user-images.githubusercontent.com/48792230/222747266-1fc5b704-c438-4792-923d-5d21dd1fe6c7.png](https://user-images.githubusercontent.com/48792230/222747266-1fc5b704-c438-4792-923d-5d21dd1fe6c7.png)
    
    - 이 상태에서 page size를 1로 잡는다면? 첫번째 행만 출력됨
        - JPA는 ‘팀A에는 회원1만 포함된다’고 인식함
        - 1대다 조인으로 뻥튀기된 데이터들을 페이징API로 DB에서 잘라올 경우 JPA가 잘려진 데이터만 받기 때문에 이를 기반으로 잘못된 정보를 내보낼 수 있기 때문
- 하이버네이트는 경고 로그를 남기고 메모리에서 페이징함
    - SQL에서 페이징한 결과를 가져오는 것이 아니라 모든 데이터를 다 출력함
    - OOM(Out Of Memory) 문제를 유발할 수 있음
- 해결방법
    - **방법1) 일대다 관계를 다대일이 되도록 바꿔줌**
    
    ```sql
    select m from Member m join fetch m.team t
    ```
    
    - **방법2) `@BatchSize` 사용**
        - [@BatchSize와 페이징의 관계?](https://www.inflearn.com/questions/247048/batchsize-%EC%99%80-%EC%BB%AC%EB%A0%89%EC%85%98-%ED%8E%98%EC%B9%98%EC%A1%B0%EC%9D%B8-%EA%B4%80%EB%A0%A8%ED%95%B4%EC%84%9C-%EC%A7%88%EB%AC%B8%EC%9D%B4-%EC%9E%88%EC%8A%B5%EB%8B%88%EB%8B%A4)
        - Team만 대상으로 조회 (1:N 페치조인은 페이징이 불가능하므로)
        
        ```sql
        select t from Team t
        ```
        
        - team을 조회한 결과에서 members를 찾으려면 지연로딩에서 추가 쿼리가 발생 → N+1문제 발생
            - ex) team을 조회하는 쿼리(1개) → 반환된 데이터가 10개라면 `select m from Member m where m.team = “(팀ID)”`로 10번 나감
        - N+1문제를 해결하는 방법 : `@BatchSize`
            - 여러 개의 프록시 객체를 조회할 때 WHERE절이 같은 여러 개의 SELECT쿼리들을 하나의 IN쿼리로 만들어줌
            - LazyLoading 된 데이터들을 사용할때마다 가져오지 않고 한꺼번에 가져옴
            - `select m from Member m where m.team = “(팀ID)”`로 10개의 쿼리가 나가는 문제를 한 방 쿼리로 해결 가능
        - 자주 사용하기 때문에 global setting을 걸어두면 좋다
            - 1000이하로 적절하게 사이즈 세팅
            
            ```xml
            <!-- persistence.xml -->
            <property name="hibernate.defalut_batch_fetch_size" value="100">
            ```
            
        - (심화) ****[default_batch_fetch_size 동작 원리](https://www.inflearn.com/questions/34469/default-batch-fetch-size-%EA%B4%80%EB%A0%A8%EC%A7%88%EB%AC%B8)****

<br>

## 페치 조인의 특징과 한계

### 1. 지연로딩 + 페치 조인 전략 사용하기

- 연관된 엔티티들을 SQL 한 번으로 조회 가능 ⇒ 성능 최적화
- 엔티티에 직접 적용하는글로벌 로딩 전략보다 우선됨
    - `@OneToMany(fetch = FetchType.LAZY)`
- 실무에서 글로벌 로딩 전략은 모두 지연로딩
- 최적화가 필요한 곳은 페치 조인을 적용하는 전략을 사용하자
- ⇒ JPA에서 발생하는 성능 문제를 대부분 해결 가능함

### 2. 모든 것을 페치 조인으로 해결할 수는 없음

- 페치 조인은 객체 그래프를 유지할 때 사용하면 효과적
- 여러 테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀 다른 결과를 내야 하면, 페치 조인보다는 `일반 조인`을 사용하고 필요한 데이터들만 조회해서 `DTO`로 반환하는 것이 효과적
