# 🍃 스프링 부트 컴포넌트 스캔 방식

- `@SpringBootApplication`이 존재하는 패키지 및 하위 패키지들이 컴포넌트 스캔의 대상이 된다.
- 스프링 빈으로 관리되며, `@Repository, @Service, @Controller`는 `@Component`를 가지고 있기 때문에 컴포넌트 스캔의 대상이 된다.

```java
@SpringBootApplication
public class JpashopApplication {
	public static void main(String[] args) {
		SpringApplication.run(JpashopApplication.class, args);
	}
}
```

---

# 🚨 생성자 주입 방식

- 스프링에서 제공하는 의존성 주입 방식은 `필드(Field) 주입`, `수정자(Setter) 주입`, `생성자(Constructor) 주입` 3가지이고, 이 중 생성자 주입 방식을 권장한다.
- 최신 스프링 버전에서 **생성자가 1개인 경우에 @Autowired 생략이 가능**하다.
- `final` 키워드를 붙이면 컴파일 시점에 초기화 여부를 확인할 수 있다
  ![final 키워드를 붙이는 이유](https://user-images.githubusercontent.com/61447654/206452542-03c00c9d-bf06-4668-b345-27f0367374a5.png)

```java
@Service
@Transactional(readOnly = true)
public class MemberService {
    private MemberRepository memberRepository;

    @Autowired
    public MemberService(MemberRepository repository) {
        this.memberRepository = repository;
    }
}
```

- `@RequiredArgsConstructor`
  - `final`이 붙거나 `@NotNull` 이 붙은 필드의 생성자를 자동으로 생성해주는 롬복 어노테이션
- 생성자 코드를 직접 작성하지 않아도 되고, 필드가 추가되도 자동으로 추가되기 때문에 매우 편리하다.

```java
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class MemberService {

    private final MemberRepository memberRepository;

}
```

---

# 👉🏻 리마인드

- `MariaDB`는 인 메모리 모드 지원 X
- [List of In-Memory Databases](https://www.baeldung.com/java-in-memory-databases)에서 인 메모리 지원 DB 확인 가능

### JPA `count()` 사용법

- JPA에서 `count 쿼리의 반환값은 Long` 타입이다.
- Integer를 써서 테스트 했을 때 아래의 에러 메세지 발생
- 파파고 돌려보니 정해진 유형이 Long이고 Integer는 호환되지 않는다는 뜻이니까 Long 타입으로 바꾸라는 뜻이다..
  `Type specified for TypedQuery [java.lang.Integer] is incompatible with query return type [class java.lang.Long]; nested exception is java.lang.IllegalArgumentException: Type specified for TypedQuery [java.lang.Integer] is incompatible with query return type [class java.lang.Long]`

`❌ 변경 전 코드`

```java
public Integer count(String name) {
	return em.createQuery("select count(m) from Member m where m.name = :name", Integer.class)
		.setParameter("name", name)
		.getSingleResult();
}
```

`✨ 변경 후 코드`

```java
public Long count(String name) {
	return em.createQuery("select count(m) from Member m where m.name = :name", Long.class)
		.setParameter("name", name)
		.getSingleResult();
}
```

### Junit5 예외 테스트 방법

- Junit4 @Test 어노테이션의 expected 속성 삭제..
- `assertThrows` : Junit5 예외 테스트 메서드
  - 작성 형식 : `assertThrows([예외 타입], [Excutable])`

`✨ Junit5`

- `message` 인자를 통해 원하는 예외가 발생되지 않았을 때 출력할 메세지 설정 가능

```java
@Test
@DisplayName("중복 회원 테스트")
void validateTest() throws Exception {
	Member member1 = new Member("member");
	Member member2 = new Member("member");

	memberService.join(member1);
	assertThrows(IllegalStateException.class, () -> {
		memberService.join(member2);
	}, "해당 예외가 발생하지 않았습니다! 예외 타입을 바꿔주세요!");
}
```

`💡 Junit4`

```java
@Test(expected = IllegalStateException.class)
@DisplayName("중복 회원 테스트")
void validateTest() throws Exception {
	Member member1 = new Member("member");
	Member member2 = new Member("member");

	memberService.join(member1);
	memberService.join(member2);

	fail("예외가 발생해야 합니다.")
}
```
