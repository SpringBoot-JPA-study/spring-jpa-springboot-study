# AOP

## AOP란?

- AOP는 Aspect Oriented Programming의 약자
- **관점 지향 프로그래밍**
    - 프로젝트 구조를 바라보는 관점을 바꿔보자는 의미
    - OOP vs AOP
        - OOP : `비즈니스 로직`의 모듈화
            - 모듈화의 핵심 단위는 비즈니스 로직
        - AOP : 인프라 혹은 `부가기능`의 모듈화
            - 대표적인 예 : 로깅, 트랜잭션, 보안 등
            - 각각의 모듈들의 주 목적 외에 필요한 부가적인 기능들
- 어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 모듈화하는 것
    - 공통관심사항 / 핵심관심사항 분리를 위함
- **애플리케이션 전체에 걸쳐 사용되는 기능을 재사용하도록 지원하는 것**

<br>

## AOP의 장점

- 애플리케이션 전체에 흩어진 공통 기능이 하나의 장소에서 관리됨
    - 유지보수 편리
- 다른 서비스 모듈들이 본인의 목적에만 충실하고 그외 사항들은 신경쓰지 않음

<br>

### AOP 활용 코드

```java
@Aspect //AOP 어노테이션
@Component
public class TimeTraceAop {

    @Around("execution(* hello.hellospring..*(..))")    //hello.hellospring패키지 하위에는 모두 적용
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        System.out.println("START : "+joinPoint.toString());
        try {
            return joinPoint.proceed();
        }finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish-start;
            System.out.println("START : "+joinPoint.toString()+" "+timeMs+"ms");

        }
    }
}
```

<br>

## [AOP 주요 개념](https://backtony.github.io/interview/2021-07-24-interview-1/#10-aop)

- Aspect : 여러 곳에 쓰이는 코드(공통 부분)를 모듈화 한 것 -> 공통 기능
- Target : Aspect가 적용되는 대상 -> 적용되는 클래스를 의미
- Advice : Aspect에서 실질적으로 어떤 일을 해야할지에 대한 실질적 부가기능을 담은 구현체
- JoinPoint : Advice가 적용될 위치 -> 즉 끼어들 지점을 의미 ex) 메서드 진입 지점, 생성자 호출 시점
- PointCut : JointPouint의 상세한 스펙을 정의한 것 ex) A란 메서드의 진입 시점에 호출할 것과 같이 구체적으로 Advice가 실행될 지점을 정의
