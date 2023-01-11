# ✨ 스프링 데이터 JPA 기초

> 실전! 스프링 데이터 JPA 강의를 듣고 정리한 내용입니다.

## ✨ 예제 도메인 모델 생성

`Member.java`

```java

@Entity
@Getter
@NoArgsConstructor(access = PROTECTED)
@ToString(exclude = {"team"})
public class Member {

    @Id
    @GeneratedValue(strategy = IDENTITY)
    private Long id;
    private String username;
    private int age;

    @ManyToOne(fetch = LAZY)
    @JoinColumn(name = "team_id")
    private Team team;

    public Member(String username) {
        this(username, 0);
    }

    public Member(String username, int age) {
        this(username, age, null);
    }

    public Member(String username, int age, Team team) {
        this.username = username;
        this.age = age;
        if (team != null) {
            changeTeam(team);
        }
    }

    public void changeTeam(Team team) {
        this.team = team;
        team.getMembers().add(this);
    }
}

```

- 기본 생성자는 열어두되, `PROTECTED`로 열어둔다.
- `@ToString`은 가급적 내부 필드만 사용하는 것이 좋다.
  - 연관 관계가 걸려있는 필드를 호출하면, 무한 참조로 인한 `StackOverflowError`가 발생한다.
  - `exclude`가 아닌 `of` 속성으로 원하는 필드를 지정해도 된다.
    **`@ToString(of = {"id", "username", "age"})`**

`Team.java`

```java
@Entity
@Getter
@NoArgsConstructor(access = PROTECTED)
@ToString(exclude = {"members"})
public class Team {

    @Id @GeneratedValue(strategy = IDENTITY)
    private Long id;
    private String name;

    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();

    public Team(String name) {
        this.name = name;
    }
}

```

## 😱 순수 JPA로 레포지토리 생성하기

`MemberJpaRepository.java`

```java
@Repository
@RequiredArgsConstructor
public class MemberJpaRepository {

    private final EntityManager em;

    public Member save(Member member) {
        em.persist(member);
        return member;
    }

    public void delete(Member member) {
        em.remove(member);
    }

    public List<Member> findAll() {
        return em.createQuery("select m from Member m", Member.class)
                .getResultList();
    }

    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(em.find(Member.class, id));
    }

    public long count() {
        return em.createQuery("select count(m) from Member m", Long.class)
                .getSingleResult();
    }

    public Member find(Long id) {
        return em.find(Member.class, id);
    }
}

```

`TeamJpaRepository.java`

```java
@Repository
@RequiredArgsConstructor
public class TeamJpaRepository {

    private final EntityManager em;

    public Team save(Team team) {
        em.persist(team);
        return team;
    }

    public void delete(Team team) {
        em.remove(team);
    }

    public List<Team> findAll() {
        return em.createQuery("select t from Team t", Team.class)
                .getResultList();
    }

    public Optional<Team> findById(Long id) {
        Team team = em.find(Team.class, id);
        return Optional.ofNullable(team);
    }

    public long count() {
        return em.createQuery("select count(t) from Team t", Long.class)
                .getSingleResult();
    }
}

```

- 순수 JPA를 이용한 레포지토리는 **`CRUD 코드가 중복`** 되는 문제를 가지고 있다.
  - 엔티티의 이름이 다른 것을 제외하면 완전히 동일한 코드이다.
- 엔티티 수정은 **`변경 감지`** 를 사용해서 수정한다.

### ✨ JpaRepository 인터페이스 적용해서 레포지토리 생성하기

- MemberRepository 인터페이스에 `JpaRepository` 인터페이스를 상속 받는다.
  - 제네릭은 **`<엔티티 타입, 식별자 타입>`** 을 작성하면 된다.
  - 인터페이스 간 상속에서는 `extends` 키워드를 사용한다.

`MemberRepository.java`

```java
import org.springframework.data.jpa.repository.JpaRepository;
import study.datajpa.member.entity.Member;

public interface MemberRepository extends JpaRepository<Member, Long> {}
```

- 상속받은 `JpaRepository` 인터페이스에 CRUD와 관련된 메서드들이 존재하기 때문에 memberRepository 인터페이스에 메서드가 없어도 정상적으로 동작한다.
- CRUD와 관련된 코드들은 `JpaRepository`가 전부 가지고 있다. → **`중복 제거`**

`MemberRepositoryTest.java`

```java
@SpringBootTest
@Transactional
public class MemberRepositoryTest {

    @Autowired
    MemberRepository memberRepository;

    @Test
    @Rollback(false)
    void entityTest() throws Exception {
        Member member = new Member("user1");
        Member saveMember = memberRepository.save(member);

        Member findMember = memberRepository.findById(member.getId()).get();

        assertThat(member.getId()).isEqualTo(1);
        assertThat(member.getUsername()).isEqualTo("user1");
        assertThat(findMember).isEqualTo(member);
    }
}

```
