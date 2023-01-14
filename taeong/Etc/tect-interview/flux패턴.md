# [flux패턴](https://beomy.tistory.com/44)

- **MVC패턴의 한계**
    - 애플리케이션이 복잡해질 수록 모델-뷰의 관계가 복잡해지는 문제 발생
    - 버그를 수정하기도 어렵고, 데이터의 흐름을 알아보기도 어려움
    - `양방향`이므로 데이터를 일관성있게 뷰에 공유하기가 어려움


    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FALrHe%2FbtqBTMSuHfN%2FZlW9i9ET34e90APgCRChk1%2Fimg.png"/>
    
    <br>
     <br>
     
- **flux패턴** : MVC 모델의 단점을 보안하기 위해 페이스북에서 발표한 아키텍쳐
    - 데이터의 흐름을 `단방향`으로 흐르게 처리하는 디자인 패턴
    - Action, Dispatcher, Store 계층이 있음
        <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FlmfPW%2FbtqBQnTPgIs%2FZ1jmHHdNcOTNiu93kQ9gMk%2Fimg.png"/>
        
        - Action : 이벤트가 발생했을 때 액션에 관한 객체를 만들어서 Dispatcher로 전달
        - Dispatcher : 전달받은 Action 객체 정보를 기반으로 어떤 행위를 할 것인가를 결정. 보통 switch문을 써서 Action객체의 타입을 기반으로 해당하는 로직을 수행
        - Store : 데이터 상태를 담고 있는 계층
    - 장점
        - 데이터 일관성 증대
        - 버그 찾기 쉬워짐
        - 단위테스트가 쉬워짐



__링크 걸려있는 블로그 댓글 읽어보면 좋음__
