# Stream이란?

- 다양한 데이터소스를 **표준화된 방법으로 다루기 위한 것**
    - 데이터 소스 : 여러 데이터를 저장하고 있는 것
        - 컬렉션, 배열..
    - 표준화된 방법 ⇒ 컬렉션 사용방법을 표준화
        - List, Set, Map.. 사용방법이 다 다름
        - 스트림이 등장하면서 사용방법이 통일됨
- 컬렉션(List, Set, Map), 배열과 같은 `데이터소스`를 `스트림`으로 만들 수 있음
    
    <img src="https://user-images.githubusercontent.com/48792230/226639582-dd2d9a44-996d-45d0-8cd9-a9cf25fd54e2.png" width="600" />
    
    <br>
    
### **코드 작성 방법 Ex**

```java
stream.distinct().limit(5).sorted().forEach(System.out::println)
```

1. 스트림만들기
    1. `list.stream()` 등
2. 중간연산 (0~n번 수행)
    1. `distinct().limit(5).sorted()`
3. 최종연산 (0~1번 수행)
    1. `forEach(System.out::println)`

<br>

### 스트림의 특징

1. __스트림은 데이터 소스로부터 데이터를 읽기만할 뿐 변경하지 않음__
    - 스트림을 생성해서 작업을 하면 원본은 그대로 있고 원본의 복사본을 반환
2.  __스트림은 Iterator처럼 일회용__
    - 필요하면 다시 스트림을 생성해야함

        ```java
        stream.forEach(System.out::println);
        //forEach 사용시 내부 요소를 꺼내면서 출력 (최종연산)

        int num = stream.count();
        //최종연산이 끝났으므로 스트림이 이미 닫힘 -> 에러 발생
        ```

3. __최종 연산 전까지 중간 연산이 수행되지 않음 (지연된 연산)__
    1. 최종연산이 될 때 중간연산이 수행됨
    2. 최종연산이 수행되지 않으면 중간연산도 x
4. __스트림은 작업을 내부 반복으로 처리할 수 있음__
5. __스트림의 작업을 병렬로 처리 가능 (멀티스레드 → 병렬 스트림)__
    1. 빅데이터를 빠른 속도로 처리하기 위해 여러 개의 스레드로 나눠서 병렬로 스트림 처리
    2. 기본은 sequential(), 병렬스트림은 parallel()로 전환 가능 
6. __기본형 스트림 - IntStream, LongStream, DoubleStream____
    1. 오토박싱&언박싱의 비효율이 제거됨
        1. Stream<Integer> 대신 IntStream 사용
    2. 숫자와 관련된 유용한 메소드를 Stream<T>보다 더 많이 제공
