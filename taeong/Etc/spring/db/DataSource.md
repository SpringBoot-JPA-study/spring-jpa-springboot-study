# DataSource

### 커넥션을 얻는 방법
- `JDBC DriverManager` 를 직접 사용
- `커넥션 풀`을 사용 (커넥션 풀이 내부적으로 JDBC DriverManager를 사용)
    
    <br>
    
### 문제 발생
- **DriverManager 를 통해서 커넥션을 획득하다가, HikariCP 커넥션 풀을 사용하는 방법으로 변경하려면?**
    - 커넥션을 획득하는 애플리케이션 코드도 함께 변경해야 한다. (OCP 위반)
    - **커넥션을 획득하는 방법을 추상화**할 필요 있음!
- 자바에서는 이런 문제를 해결하기 위해 `javax.sql.DataSource` 라는 인터페이스를 제공
  - 이 인터페이스의 핵심 기능은 **커넥션 조회** 하나 (다른 일부 기능도 있지만 크게 중요하지 않다.)
  - 어떻게 커넥션을 제공하는지는 관심이 없어! 그냥 커넥션만 줘!
    
    <br>
   
### 정리

- **대부분의 커넥션 풀은 DataSource 인터페이스를 이미 구현**해두었기 때문에 개발자는 DBCP2 커넥션 풀, HikariCP 커넥션 풀 의 코드를 직접 의존하는 것이 아니라 DataSource 인터페이스에만 의존하도록 애플리케이션 로직을 작성하면 됨
- 그러나 **DriverManager 는 DataSource 인터페이스를 사용하지 않기** 때문에 DriverManager 는 직접 사용해야 한다. (DriverManager 를 사용하다가 DataSource 기반의 커넥션 풀을 사용하도록 변경하면 관련 코드를 다 고쳐야 함)
    - 이런 문제를 해결하기 위해 스프링은 **DriverManager 도 DataSource 를 통해서 사용할 수 있도록** **DriverManagerDataSource 라는 DataSource 를 구현**한 클래스를 제공
    
    <br>
   
💡 **DriverManagerDataSource 를 통해서 DriverManager 를 사용하다가 커넥션 풀을 사용하도록 코드를 변경해도 애플리케이션 로직은 변경하지 않아도 됨!**

