## 좋은 객체 지향 설계의 5가지 원칙 (SOLID)

- `SRP` : **단일 책임 원칙(single responsibility principle)**
    - 하나의 클래스는 하나의 책임만 가져야한다.
        - 클래스의 범위는 특정 기능을 수정할 때 소속된 클래스만 수정할 수 있는 규모로 결정한다. 
        - (너무 크거나 너무 크다면 x)

<br>

- `OCP` : **개방-폐쇄 원칙 (Open/closed principle)**
    - 확장에는 열려있으나 변경에는 닫혀있어야 한다.
    - OCP원칙을 지키는 것이 가능한가?
        - 구현 객체를 변경하려면 클라이언트 코드를 변경해야 한다.
        - ⇒ 분명 다형성을 사용했지만 OCP 원칙을 지킬 수 없다.  
        - 그럼 OCP 원칙을 지키는 것이 가능한가? → 가능하긴하다!
        - 객체를 생성하고, 연관관계를 맺어주는 별도의 조립, 설정자가 필요하다.
        - ⇒ `스프링 컨테이너` 가 그 역할을 수행한다.

<br>

- `LSP` : **리스코프 치환 원칙 (Liskov substitution principle)**
    - 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.
        - 예) 자동차 인터페이스의 엑셀은 앞으로 가라는 기능이므로 뒤로 가게 구현하면 LSP 위반이다. 느리더라도 앞으로 간다면 LSP를 준수한다.

<br>

- `ISP` : **인터페이스 분리 원칙 (Interface segregation principle)**
    - 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.
        - 분리할수록 인터페이스가 명확해지고, 대체 가능성이 높아진다.

<br>

- `DIP` : **의존관계 역전 원칙 (Dependency inversion principle)**
    - 의존 관계를 맺을 때, 변하기 쉬운 것 (구체적인 것) 보다는 변하기 어려운 것 (추상적인 것)에 의존해야 한다.
        - 클라이언트가 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻
        - DIP 위반 예시 ) 인터페이스에 의존하면서 동시에 구현클래스에도 동시에 의존할 수 있다.
            
            ```java
            // MemberRepository : 인터페이스
            // MemoryMemberRepository : MemberRepository를 구현한 클래스
            MemberRepository m = new MemoryMemberRepository();
            ```
