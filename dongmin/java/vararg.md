# 가변인자 (Variable Argument)

> 👉🏻 하나의 메서드에서 매개변수를 동적으로 받을 수 있는 기능

- `JDK 1.5` 부터 도입된 기능으로, 이전 버전에서는 배열이나 컬렉션을 사용해서 동적 매개변수를 작성했다.
- 가변인자는 내부적으로 배열을 생성해서 사용하기 때문에 사용에 주의해야한다.
  - 메서드 호출 시마다 배열이 새로 생성되기 때문에 남발하면 안됨
- **`가변 인자 외에도 매개 변수가 더 있다면 가변 인자를 마지막에 작성해야한다.`**
  - 가변 인자인지 아닌지를 구별할 방법이 없기 때문에 컴파일 에러가 발생한다.
    👉🏻 _`Vararg parameter must be the last in the list`_
  - 타입이 다른 매개 변수인 경우에도 허용되지 않는다.
- 가변 인자를 사용한 메서드는 오버로딩하지 않는 것이 좋다.

`❌ bad`

```java
private static void variable(String...str, int age) {
    System.out.println(Arrays.toString(str));
}
```

`✨ cool`

```java
private static void variable(int age, String...str) {
    System.out.println(Arrays.toString(str));
}
```

## 매개변수 타입을 배열로 받으면 되는거 아닌가?

- 매개 변수를 배열로 받을 경우 값이 필요없는 경우에도 빈 배열을 생성해서 넣어줘야한다.  
  👉🏻 가변 인자는 이름 그대로 동적 매개변수이기 때문에 값을 넣지 않아도 상관없다.
- 아래 코드에서 가변 인자를 받는 메서드의 결과값은 빈 배열이고, 배열을 받는 메서드는 배열 매개변수가 없기 때문에 실행조차 되지 않는다.

```java
public static void main(String[] args) {
    variable();
    variable2(); // 컴파일 에러!!
}

private static void variable(String...str) {
    System.out.println(Arrays.toString(str));
}

private static void variable2(String[] str) {
    System.out.println(Arrays.toString(str));
}
```
