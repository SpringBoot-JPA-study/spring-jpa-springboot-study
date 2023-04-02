# 의존성 주입(DI)이란?

## DI가 없다면?

![https://user-images.githubusercontent.com/48792230/227774942-aa5ebf71-7e7a-48af-a757-24a61270d482.png](https://user-images.githubusercontent.com/48792230/227774942-aa5ebf71-7e7a-48af-a757-24a61270d482.png)

- OrderServiceImpl이 DiscountPolicy(인터페이스)와 FixDiscountPolicy(구현체)를 둘 다 의존하고 있음
    - 구현체를 의존하고 있으므로 DIP 위반
- FixDiscountPolicy에서 RateDiscountPolicy로 변경하는 경우,   결국 OrderServiceImpl에서 RateDiscountPolicy를 의존하도록 수정이 필요함
    - OCP 위반

<br>

## DIP와 OCP를 위반하지 않게 하려면?

- DIP를 위반하지 않으려면 **인터페이스에만 의존하도록** 의존관계를 변경하면 됨
- **클라이언트 코드가 변경되지 않으면** OCP가 지켜진다고 봄 (설정을 위한 최소한의 수정은 필요)
- EX) DIP 위반 코드 변경 사례
    - MemberRepository : 인터페이스
    - MemoryMemberRepository : MemberRepository를 구현한 클래스
    - **DIP 위반 코드**
        
        ```java
        
        public class MemberServiceImpl implements MemberService{
        
        	private final MemberRepository memberRepository = new MemoryMemberRepository();
        	...
        }
        ```
        
        - MemberServiceImpl가 memberRepository와 MemoryMemberRepository를 모두 의존
    - **DIP를 위반하지 않도록 변경한 코드**
        
        ```java
        public class MemberServiceImpl implements MemberService{
        
        	private final MemberRepository memberRepository;
        	
        	public MemberServiceImpl(MemberRepository memberRepository) {
        	    this.memberRepository = memberRepository;
        	}
        	...
        }
        ```
        
        - MemberServiceImpl가 memberRepository의 구현체를 모름 (only 인터페이스에만 의존)
        - 생성자를 통해 외부에서 memberRepository 구현체를 받음 ⇒ **의존성 주입(Dependency Injection)**
    - **주입해주는 역할을 AppConfig 클래스에서 수행**
        
        ```java
        public class AppConfig {
        	public MemberService memberService() {  //생성자 주입
        	    return new MemberServiceImpl(new MemoryMemberRepository());
        	}
        }
        ```
        
        - AppConfig  : 애플리케이션의 실제 동작에 필요한 **구현 객체를 생성**하는 클래스
        - 생성자 주입 : 생성한 객체 인스턴스의 참조를 **생성자를 통해서 주입**해줌
    - **MemberService를 사용한 클라이언트 코드**
        - DI 적용 전
            
            ```java
            public static void main(String[] args) {
            	MemberService memberService = new MemoryMemberRepository();
            	...
            }
            ```
            
            - 인터페이스를 갈아 끼우려할 때 기존 코드에 변경이 일어남
            - 클라이언트 코드에서 너무 많은 역할을 하고 있었던 것 (사실상 SRP도 위반)
        - DI 적용 후
            
            ```java
            public static void main(String[] args) {
            	AppConfig appConfig = new AppConfig();
            	MemberService memberService = appConfig.memberService();
            	...
            }
            ```
            
            - AppConfig을 제외한 기존 코드에 변경이 일어나지 않고 인터페이스를 갈아 끼울 수 있음
            - AppConfig의 코드만 수정해주면 됨

<br>

## DI 적용 효과

![https://user-images.githubusercontent.com/48792230/227774939-e1ce4d92-df22-418f-8205-5d3f675fd282.png](https://user-images.githubusercontent.com/48792230/227774939-e1ce4d92-df22-418f-8205-5d3f675fd282.png)

- **AppConfig를 통해서 관심사를 확실하게 분리 가능**
    - AppConfig는 구체 클래스를 선택하는 역할
        - 애플리케이션이 어떻게 동작해야 할지 전체 구성을 책임지는 역할을 수행
    - 각 클래스들은 담당 기능을 실행하는 책임만 지면 됨
- **클라이언트의 코드는 변경이 일어나지 않음**
