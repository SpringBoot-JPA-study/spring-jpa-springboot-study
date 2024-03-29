# static

[[Java] static변수와 static 메소드](https://mangkyu.tistory.com/47) 참고

- Static 키워드를 사용한다 = 메모리에 한번 할당되어 프로그램이 종료될 때 해제됨
    

### **Static의 메모리**

- Class는 `Static 영역`에 생성
- new 연산을 통해 생성한 객체는 `Heap영역`에 생성
    - Heap영역의 메모리는 `Garbage Collector`를 통해 수시로 관리
- Static 키워드를 통해 Static 영역에 할당된 메모리는 모든 객체가 공유하는 메모리
    - **Garbage Collector의 관리 영역 밖에 존재하므로 Static을 자주 사용하면 프로그램의 종료시까지 메모리가 할당된 채로 존재**
    - 결국 static을 남발하지 말라는 이유는 **가비지 컬렉터의 관리를 받지 못해서 프로그램 종료까지 계속 쌓이기 때문이다. → 시스템 성능에 악영향**

      
<br>
 

### 1. **Static 변수(정적 변수)**

1. **Static 변수 특징**
- Static 변수는 **클래스 변수**이다.
    - 메모리에 고정적으로 할당되어, 프로그램이 종료될 때 해제되는 변수이다.
    - 여러 객체가 해당 메모리를 공유할 수 있다.
- **객체를 생성하지 않고도 Static 자원에 접근이 가능**하다.
    - 객체가 생성되기 이전에 이미 할당이 되어 있기 때문에 객체의 생성없이 바로 접근(사용)할 수 있다.
   
<br>

### 2. static 메소드

- 일반적으로는 **유틸리티 관련 함수**들은 **여러 번 사용**되므로 static 메소드로 구현을 하는 것이 적합
- static 메소드를 사용하는 대표적인 Util Class : java.uitl.Math
- static 메소드에서 접근하기 위한 변수는 반드시 static 변수로 선언되어야 합니다.
- static한 변수에 접근하려면 반드시 static메서드가 되어야 한다 (x) → 인스턴스 메소드로 static 변수에 접근이 가능함! 그러나 static 메소드에 접근하려면 static 변수로 선언되어야 함
- 인스턴스 메소드 안의 변수들(지역 변수)는 메소드가 호출될 때 생성이 되므로 메소드 안의 변수에서는 static 선언이 불가능함
- static 메소드와 인스턴스(non-static) 메소드의 차이점
    - static 영역에 있는 메소드라면 객체의 생성 없이 클래스를 통해서 바로 호출 가능
    - static 메소드가 아니라면 객체를 통해서만 접근 가능
   
<br>

### 3. **실제 Static 변수와 Static 메소드의 활용**

**1) Static 변수**

- 일반적으로 상수들만 모아서 사용하며 상수의 변수명은 **대문자와 _를 조합**하여 이름짓는다.
- **상속을 방지**하기 위해 `final class`로 선언을 한다.
  - static 변수의 값이 바뀌게 되면 static 변수를 참조하고 있는 다른 코드에서 문제가 발생할 수 있기 때문에 그걸 막으려는 의도


**2) Static 메소드**

- 상속을 방지하기 위해 `final class`로 선언
- 유틸 관련된 함수들을 모아둔다.
