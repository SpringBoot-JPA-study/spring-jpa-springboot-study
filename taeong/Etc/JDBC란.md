# JDBC란?

### JDBC 표준 인터페이스

> JDBC(Java Database Connectivity)는 자바에서 **데이터베이스에 접속할 수 있도록 하는 자바 API**다.
> 

JDBC는 데이터베이스에서 자료를 쿼리하거나 업데이트하는 방법을 제공한다.

![https://user-images.githubusercontent.com/48792230/233791434-40b65be2-ec74-4a2e-9dcb-b8788245a962.png](https://user-images.githubusercontent.com/48792230/233791434-40b65be2-ec74-4a2e-9dcb-b8788245a962.png)

대표적으로 다음 3가지 기능을 표준 인터페이스로 정의해서 제공한다.

- java.sql.Connection - 연결
- java.sql.Statement - SQL을 담은 내용
- java.sql.ResultSet - SQL 요청 응답

그런데 인터페이스만 있다고해서 기능이 동작하지는 않는다. 이 JDBC 인터페이스를 각각의 `DB 벤더`
(회사 : MySQL, Oracle…)에서 자신의 DB에 맞도록 **구현해서 라이브러리로 제공**하는데, 이것을 `JDBC 드라이버`라 한다. 

- 예를 들어서 MySQL DB에 접근할 수 있는 것은 MySQL JDBC 드라이버라 하고, Oracle DB에 접근할 수 있는 것은 Oracle JDBC 드라이버라 한다

개발자는 JDBC 표준 인터페이스만 맞춰서 애플리케이션 로직을 작성하면 됨 → JDBC드라이버 종류만 정해주면 구현체가 적용되어 동작함
