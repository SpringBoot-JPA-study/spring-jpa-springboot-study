# 1주차 - 프로젝트 환경설정

👉🏻 [ 실습 코드 확인](https://github.com/ddmkim94/spring-jpa-roadmap/tree/master/spring-jpa-1)

## ✅ 실습 프로젝트 환경

|     이름      |    버전    |
| :-----------: | :--------: |
|    `Java`     |   **17**   |
| `Spring Boot` | **2.7.6**  |
|   `MariaDB`   | **10.9.2** |

- 스프링 부트 버전에 따라 라이브러리 버전이 달라진다.
  - 해당 라이브러리 + 하위 라이브러리를 함께 가져오기 때문에 환경 설정이 매우 편리하다.

## ✨ 프로젝트 생성하기

[스프링 프로젝트 생성](https://start.spring.io/)

<img width="1440" alt="스크린샷 2022-12-02 오후 12 18 53" src="https://user-images.githubusercontent.com/61447654/205227700-25225433-9952-4909-b9ae-2640e9ffa9af.png">

### 추가 설정

- `p6spy`: 쿼리 파라미터 로그 형식으로 출력 **[ [p6spy 링크](https://github.com/gavlyukovskiy/spring-boot-data-source-decorator) ]**

  - 개발 단계에서는 마음껏 사용해도 괜찮다.
  - 운영 단계에서는 반드시 성능 테스트 후 도입을 고려해야한다.

  <img width="980" alt="스크린샷 2022-12-02 오후 2 48 36" src="https://user-images.githubusercontent.com/61447654/205230578-74218539-cb81-40ef-802f-8e497f7a353f.png">

- `validation`: 유효성 검증
- `devtools`: 개발 생산성 향상 `(대표적으로 Live Reload)`

```java
// build.gradle
implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.8.1'
implementation 'org.springframework.boot:spring-boot-starter-validation'
developmentOnly 'org.springframework.boot:spring-boot-devtools'
```

## 🍃 Thymeleaf 불러오기

- 스프링 부트에서는 JSP보다 타임 리프를 쓰는 것을 권장하고 있다.
- JSP는 사장되기 추세니까 굳이 쓸 필요없을듯..

```java
@Controller
public class HomeController {

  @GetMapping("/")
  public String home() {
    return "home";
  }
}
```

- 스프링 부트는 기본적으로 String을 반환하면 해당 이름을 가진 html 파일을 호출한다.
- 기본 매핑: `resources:templates/+[viewName]+.html`
- 매핑 정보는 수정해서 사용할 수 있지만 관례를 따르는 것이 좋다.

## 🏃🏻 JPA, DB 동작 확인

### application.yml 설정 파일 추가

> 추가 구현

- `공통 yml`과 `개발용 yml`을 나눠서 작성했다.
- 깃허브에 보안에 민감한 데이터가 올라가는 것을 막기 위해서 나눠서 작성했다.

> **application.yml**

```yml
spring:
  profiles:
    active: dev
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver

  jpa:
    properties:
      hibernate:
        format_sql: true

logging.level:
  jpabook.jpashop: debug
  org.hibernate.SQL: debug
```

> **application-dev.yml**

```yml
server:
  port: 8020
spring:
  datasource:
    url: jdbc:mariadb://127.0.0.1:3306/spring_jpa_1db
    username: root
    password: root

  jpa:
    hibernate:
      ddl-auto: create
```

### 회원 엔티티 개발

> 추가 구현

- JPA Auditing 기능을 사용해서 `식별자`, `생성일`, `수정일`을 자동화

> BaseEntity.java

```java
@Getter
@MappedSuperclass
@NoArgsConstructor
@AllArgsConstructor
@SuperBuilder
@EntityListeners(AuditingEntityListener.class)
public class BaseEntity {

    @Id
    @GeneratedValue(strategy = IDENTITY)
    private Long id;

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime updatedDate;
}

```

> Member.java

```java
@Entity
@Getter
@NoArgsConstructor
@AllArgsConstructor
@SuperBuilder
public class Member extends BaseEntity {

    private String username;

    public void changeName(String newName) {
        this.username = newName;
    }
}
```

> 테스트 코드

```java
@SpringBootTest
@Transactional // 테스트 클래스에서는 테스트 종료 후 데이터를 롤백해주는 역할을 한다.
class MemberRepositoryTest {

    @Autowired
    MemberRepository memberRepository;

    @PersistenceContext
    EntityManager em;

    @Test
    @Rollback(false)
    void testMember() throws Exception {
        Member member = Member.builder()
                .username("이강인")
                .build();

        Long saveId = memberRepository.save(member);

        Member findMember = memberRepository.find(saveId);
        findMember.changeName("손흥민");

        // Auditing을 통해 update된 시간은 바로 적용이 안됨..
        em.flush();
        em.clear();

        assertThat(findMember.getId()).isEqualTo(member.getId());
        assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
        assertThat(findMember).isEqualTo(member);
        assertThat(findMember.getUpdatedDate()).isAfter(findMember.getCreatedDate());
    }
}
```

<img width="528" alt="스크린샷 2022-12-02 오후 3 52 53" src="https://user-images.githubusercontent.com/61447654/205234052-d8c54532-74c5-4377-8094-a3fc552908de.png">

- 생성일과 수정일이 다른 것을 확인할 수 있다.

## 💡 리마인드

- 실무에서는 Spring Data JPA를 쓰지만 JPA를 이해하고 있어야 보다 더 잘 쓸 수 있다.
- 엔티티 매니저를 통한 모든 데이터 변경은 트랜잭션 안에서 이루어져야 한다.
  - 조회는 트랜잭션 여부 상관없다.
- 테스트 클래스에서 `@Transactional`은 반복적인 테스트 실행을 위해 테스트 종료 후 데이터를 롤백한다.
- 운영 단계에서 `System.out`은 쓰면 안된다. 반드시 `logger`를 통해서 남겨야한다.

## 👉🏻 몰랐던 내용

- `JPA Auditing`을 사용할 때 변경된 날짜는 db에만 반영되기 때문에 영속성 컨텍스트와의 데이터 불균형이 발생할 수 있다.
  - 두 저장소를 동기화하는 과정이 반드시 필요하다. `(flush)`
