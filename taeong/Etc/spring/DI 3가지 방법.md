# DI 3가지 방법(필드 주입, setter 주입, 생성자 주입)

## 필드 주입

```java
@Autowired
private final MemberService memberService;
```

- 코드가 간결하지만 외부에서 변경이 불가능해서 테스트 하기 힘듦
    - 엔터프라이즈 환경을 위해 개발된 스프링에서 테스트가 어렵다는 것은 치명적인 단점
- DI 프레임워크가 없으면 아무것도 할 수 없음
    - MemberService에 구현체를 연결하지 않았으므로 NULLPointException 발생
    - 결국 의존관계를 주입하려면 Setter의 힘을 빌려야 함 → 결국 코드가 길어짐
- 애플리케이션의 실제 코드와 관계 없는 테스트 코드 혹은 스프링 설정을 목적으로 하는 @Configuration 같은 곳에서만 특별한 용도만로 사용

<br>

## setter 주입

```java
@Controller
public class MemberController {

    private MemberService memberService;

    @Autowired
    public void setMemberService(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

- 빈을 등록한 이후에 의존관계 주입이 별도로 일어남
- setMemberService() 자체는 스프링 실행 이후에 다 세팅이 되고 나면 변경될 필요가 없음
    - 근데 setter의 특성상 변경이 가능함(public하게 노출되어있음)
    - 이후에 잘못 바꾸면 문제가 발생하기 때문에 불변으로 해놓는게 좋음
- 그러므로 선택, 변경 가능성이 있는 의존관계에 사용

<br>

## 생성자 주입

```java
@Controller
public class MemberController {
    private final MemberService memberService;

    @Autowired
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }

}
```

- ‘생성자’이기 때문에 빈을 등록하면서 의존관계 주입도 같이 일어남
- 스프링 컨테이너 세팅되는 시점에 한 번 호출되고 끝남(불변)
- 의존관계가 실행중에 동적으로 변하는 경우는 아예 없으므로 생성자 주입을 권장
    - 의존관계가 바뀌어야 한다면 아예 서버를 새로 실행함 (실행 중에 동적으로 바뀌는게 아님)
- 무조건 값이 세팅되어야 한다는 의미로 final 키워드를 사용함
