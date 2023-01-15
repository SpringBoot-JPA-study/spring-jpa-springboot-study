# Spring MVC패턴 적용사례

Spring Web MVC에서는 ‘DispatcherServlet의 요청 처리과정’에 MVC패턴이 적용되었다고 말할 수 있음

<img src="https://blog.kakaocdn.net/dn/cur53C/btrgu9t392q/bnILkLIHGtPJt0eXxdQXC0/img.png"  height="400"/>

**DispatcherServlet의 요청 처리과정 (위의 이미지 순서와 상관없음)**

1. 클라이언트의 모든 요청은 프론트 컨트롤러인 DispatcherServlet이 받게 됨
2. DispatcherServlet은 HandlerMapping을 참고해서 어떤 컨트롤러에 작업을 넘겨줄지 결정함
3. 처리요청을 받은 컨트롤러는 service → repository → db를 거쳐서 비즈니스 로직을 수행함
4. 사용자에게 전달할 모델 생성
5. 뷰 구현을 위해 view resolver를 참고
6. 뷰 렌더링
7. 처리결과 출력
