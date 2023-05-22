# 3강. View 환경설정

→ View 만들 때, 타임리프 사용할 거임

……

---

# <1> 타임리프

- 정보소스 (타임리프)
    
    
    [Thymeleaf](https://www.thymeleaf.org/)
    

## [ 1 ] 개요

- 자바 템플릿 엔진 (XML/XHTML/HTML5) → 당연히 웹 템플릿 엔진으로 사용가능
- 서버사이드 페이지 렌더링 방식
- 스프링 프레임워크와 잘 맞는다.
    
    (JSP(1999년)가 스프링(2003) 이전부터 있던거라서, 스프링은 타임리프를 밀고 있다.)
    

## [ 2 ] 특징 및 장단점

- ***Natural templates***
    - 마크업 구조를 깨지 않고, 그 안에서 쓸 수 있다.
    - 따라서 웹브라우저에서도 그대로 열린다. (JSP는 안열림. 산출한 웹페이지를 열어야함)

- 단점은?
    - 태그사용규칙이 html에 비해서 좀 딱딱하다. (타임리프 버전이 업되어서 해결되가고 있음)
    - 메뉴얼 좀 보면서 사용해야한다는 어려움이 있을 수 있다.

……

---

# <2> 타임리프 작동 확인

## [ 1 ] 샘플용 타임리프 코드 (hello.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Hello 페이지</title>
    <meta http-equiv="Content-Type" content="text/html" charset="UTF-8">
</head>
<body>
    <p th:text="'안녕하세요. '+ ${data}"> 안녕하세요. 손님</p>
</body>
</html>
```

## [ 2 ] 샘플용 컨트롤러 (HelloController)

```java
package jpabook.jpashop;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {

    @GetMapping("hello")
    public String hello(Model model){
        // Model
        /*
            Model 컨트롤러와 View사이의 데이터 담는 것.
            ModelAndView 도 있었지???ㅋ
         */
        model.addAttribute("data","hello!!! LSE");
        return "hello";
        // 스프링부트 타임리프의 viewName 매핑
        /*
            resources/templates 폴더안에서 return한 viewName에 맞는 html을 찾아줌
        */
    }
}
```

- 작동결과 스크린샷
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fb381d62-83b5-46d0-88a6-868c4f1a9548/Untitled.png)
    

## [ 3 ] 기타 :  스프링부트 타임리프 viewName 매핑

```java
// 컨트롤러의 이 부분을 알아서 해줌

return "hello";  // ---> 스프링부트 타임리프가 알아서 hello.html을 찾아줌
```

……

---

# <3> 팁 템플릿 수정 관련 팁

**템플릿 바꿀 때마다,  서버 리스타트 해야하는 어려움 해결**

**→ devtools 라이브러리를 사용** 

```java
// devtools 라이브러리 -> build파일 수정하고 임포트
implementation 'org.springframework.boot:spring-boot-devtools'
```

- 스크린샷
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cd6e78e1-bec0-49d4-96a4-3a7bca386bfc/Untitled.png)
    

---

페이지끝
