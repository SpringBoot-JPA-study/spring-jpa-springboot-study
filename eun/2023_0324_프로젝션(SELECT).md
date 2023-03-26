# 프로젝션(SELECT)
---
## 프로젝션 정의

SELECT절에 조회활 대상을 지정하는 것

### 엔티티 프로젝션

- SELECT m FROM Member m
- SELECT m.team FROM Member m

### 임베디드 타입 프로젝션

- SELECT m.address FROM Member m

### 스칼라 타입 프로젝션

- SELECT m.username, m.age FROM Member m

### DISTINCT로 중복제거

---

## TypeQuery, Query

- TypeQuery :  반환 타입이 명확할 때 사용
- Query :   반환 타입이 명확하지 않을 때 사용

---

## 프로젝션 - 여러값 조회

SELECT m.username, m.age FROM Member m

- 1. Query 타입으로 조회
- 2. Object[] 타입으로 조회
- 3. new명령어로 조회
    - SELECT new 패키지주소.생성자명() FROM Member m
    - 패키지 명을 포함한 전체 클래스명 입력 (걍 텍스트로서 정보가 날라가기 때문에 직접 써주어야한다.)
    - 순서와 타입이 일치하는 생성자 필요
