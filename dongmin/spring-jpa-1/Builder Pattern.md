# 주요 내용 정리

> 주문 도메인 개발 파트를 들으면서 정리한 내용 입니다.

- 좋은 테스트는 DB나 스프링 없이 순수하게 메서드를 단위 테스트 하는 것이 좋은 테스트이다.
- 테스트는 순서가 보장되지 않기 때문에, 순서에 상관없이 독립적으로 실행하는 테스트를 작성해야한다.
- `Mockito`: 단위 테스트를 위한 자바 모킹 프레임워크로 단위 테스트 원칙에 위배되는 의존성을 가짜 객체(Mock Object)로 만들어 의존하지 않도록 하는 역할을 한다.

## 😱 Builder 패턴을 이용해서 객체 생성 시 NullPointerException 처리 방법

- 주문 기능을 테스트할 때 Member 엔티티의 List에서 `NullPointerException` 발생!

```
java.lang.NullPointerException: Cannot invoke "java.util.List.add(Object)" because the return value of "jpabook.jpashop.member.entity.Member.getOrders()" is null
```

- NULL 처리를 위해 필드 레벨에서 미리 초기화를 해줬는데도 예외가 발생해서 당황했는데 알고보니 빌더 패턴의 특성 때문에 예외가 발생한 거..
- 빌더 패턴으로 객체를 생성할 때 값을 설정하지 않은 필드들은 타입의 초기값으로 초기화가 진행된다. **`(이때, 미리 지정해준 값은 무시된다.)`**
- 참고로, 생성자, 수정자를 통해 객체를 초기화하는 경우 미리 지정해준 값으로 값이 세팅된다. `(강의에서는 setter를 통해 초기화)`

```java
@Entity
@Getter
@NoArgsConstructor
@AllArgsConstructor
@SuperBuilder
public class Member extends BaseEntity {

    private String name = "동민";

    @Embedded
    private Address address
                = new Address("서울", "동대문구", "휘경동");

    @OneToMany(mappedBy = "member")
    private List<Order> orders = new ArrayList<>();
}
```

- 위의 코드처럼 필드에 값을 초기화 한 상태에서 테스트를 진행한다.

```java
@Test
public void 객체생성테스트() throws Exception {
    Member member1 = new Member();
    Member member2 = Member.builder().build();

    assertThat(member1.getName()).isEqualTo("동민");
    assertThat(member1.getAddress()).isNotNull();
    assertThat(member1.getOrders()).isNotNull();

    assertThat(member2.getName()).isNull();
    assertThat(member2.getAddress()).isNull();
    assertThat(member2.getOrders()).isNull();
}
```

- 생성자를 통해 생성한 `member1`은 미리 세팅한 값이 들어있지만, 빌더 패턴으로 생성한 `member2`는 미리 세팅한 값이 아닌 타입에 맞는 초기값이 들어있는걸 확인할 수 있다.

```java
@Builder.Default
@OneToMany(mappedBy = "member")
private List<Order> orders = new ArrayList<>();
```

- 매번 빌더로 객체 생성 시 초기화해주는 것은 번거롭기도 하고, 놓칠 수 있다.
- `@Builder.Default`을 통해 미리 명시한 기본값을 사용할 수 있습니다.
