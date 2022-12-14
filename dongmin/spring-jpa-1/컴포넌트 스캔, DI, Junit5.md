# π μ€νλ§ λΆνΈ μ»΄ν¬λνΈ μ€μΊ λ°©μ

- `@SpringBootApplication`μ΄ μ‘΄μ¬νλ ν¨ν€μ§ λ° νμ ν¨ν€μ§λ€μ΄ μ»΄ν¬λνΈ μ€μΊμ λμμ΄ λλ€.
- μ€νλ§ λΉμΌλ‘ κ΄λ¦¬λλ©°, `@Repository, @Service, @Controller`λ `@Component`λ₯Ό κ°μ§κ³  μκΈ° λλ¬Έμ μ»΄ν¬λνΈ μ€μΊμ λμμ΄ λλ€.

```java
@SpringBootApplication
public class JpashopApplication {
	public static void main(String[] args) {
		SpringApplication.run(JpashopApplication.class, args);
	}
}
```

---

# π¨ μμ±μ μ£Όμ λ°©μ

- μ€νλ§μμ μ κ³΅νλ μμ‘΄μ± μ£Όμ λ°©μμ `νλ(Field) μ£Όμ`, `μμ μ(Setter) μ£Όμ`, `μμ±μ(Constructor) μ£Όμ` 3κ°μ§μ΄κ³ , μ΄ μ€ μμ±μ μ£Όμ λ°©μμ κΆμ₯νλ€.
- μ΅μ  μ€νλ§ λ²μ μμ **μμ±μκ° 1κ°μΈ κ²½μ°μ @Autowired μλ΅μ΄ κ°λ₯**νλ€.
- `final` ν€μλλ₯Ό λΆμ΄λ©΄ μ»΄νμΌ μμ μ μ΄κΈ°ν μ¬λΆλ₯Ό νμΈν  μ μλ€
  ![final ν€μλλ₯Ό λΆμ΄λ μ΄μ ](https://user-images.githubusercontent.com/61447654/206452542-03c00c9d-bf06-4668-b345-27f0367374a5.png)

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
  - `final`μ΄ λΆκ±°λ `@NotNull` μ΄ λΆμ νλμ μμ±μλ₯Ό μλμΌλ‘ μμ±ν΄μ£Όλ λ‘¬λ³΅ μ΄λΈνμ΄μ
- μμ±μ μ½λλ₯Ό μ§μ  μμ±νμ§ μμλ λκ³ , νλκ° μΆκ°λλ μλμΌλ‘ μΆκ°λκΈ° λλ¬Έμ λ§€μ° νΈλ¦¬νλ€.

```java
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class MemberService {

    private final MemberRepository memberRepository;

}
```

---

# ππ» λ¦¬λ§μΈλ

- `MariaDB`λ μΈ λ©λͺ¨λ¦¬ λͺ¨λ μ§μ X
- [List of In-Memory Databases](https://www.baeldung.com/java-in-memory-databases)μμ μΈ λ©λͺ¨λ¦¬ μ§μ DB νμΈ κ°λ₯

### JPA `count()` μ¬μ©λ²

- JPAμμ `count μΏΌλ¦¬μ λ°νκ°μ Long` νμμ΄λ€.
- Integerλ₯Ό μ¨μ νμ€νΈ νμ λ μλμ μλ¬ λ©μΈμ§ λ°μ
- ννκ³  λλ €λ³΄λ μ ν΄μ§ μ νμ΄ Longμ΄κ³  Integerλ νΈνλμ§ μλλ€λ λ»μ΄λκΉ Long νμμΌλ‘ λ°κΎΈλΌλ λ»μ΄λ€..
  `Type specified for TypedQuery [java.lang.Integer] is incompatible with query return type [class java.lang.Long]; nested exception is java.lang.IllegalArgumentException: Type specified for TypedQuery [java.lang.Integer] is incompatible with query return type [class java.lang.Long]`

`β λ³κ²½ μ  μ½λ`

```java
public Integer count(String name) {
	return em.createQuery("select count(m) from Member m where m.name = :name", Integer.class)
		.setParameter("name", name)
		.getSingleResult();
}
```

`β¨ λ³κ²½ ν μ½λ`

```java
public Long count(String name) {
	return em.createQuery("select count(m) from Member m where m.name = :name", Long.class)
		.setParameter("name", name)
		.getSingleResult();
}
```

### Junit5 μμΈ νμ€νΈ λ°©λ²

- Junit4 @Test μ΄λΈνμ΄μμ expected μμ± μ­μ ..
- `assertThrows` : Junit5 μμΈ νμ€νΈ λ©μλ
  - μμ± νμ : `assertThrows([μμΈ νμ], [Excutable])`

`β¨ Junit5`

- `message` μΈμλ₯Ό ν΅ν΄ μνλ μμΈκ° λ°μλμ§ μμμ λ μΆλ ₯ν  λ©μΈμ§ μ€μ  κ°λ₯

```java
@Test
@DisplayName("μ€λ³΅ νμ νμ€νΈ")
void validateTest() throws Exception {
	Member member1 = new Member("member");
	Member member2 = new Member("member");

	memberService.join(member1);
	assertThrows(IllegalStateException.class, () -> {
		memberService.join(member2);
	}, "ν΄λΉ μμΈκ° λ°μνμ§ μμμ΅λλ€! μμΈ νμμ λ°κΏμ£ΌμΈμ!");
}
```

`π‘ Junit4`

```java
@Test(expected = IllegalStateException.class)
@DisplayName("μ€λ³΅ νμ νμ€νΈ")
void validateTest() throws Exception {
	Member member1 = new Member("member");
	Member member2 = new Member("member");

	memberService.join(member1);
	memberService.join(member2);

	fail("μμΈκ° λ°μν΄μΌ ν©λλ€.")
}
```
