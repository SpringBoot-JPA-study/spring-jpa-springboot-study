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
- 일대다는 데이터 뻥튀기의 위험이  있기 때문에 페이징 API 사용 불가

+ 강의 마저 듣고 내용 추가하기
