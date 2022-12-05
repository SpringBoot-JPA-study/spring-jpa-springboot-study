## **< 회차 :  라이브러리 살펴보기 >**

### **\[1\] 라이브러리 살펴보는 방법**

**라이브러리 살펴보는 방법**

\* 커맨드창 이용 방법

\* gradle창 이용 방법

**커맨드창 이용 방법**

(1) 해당 폴더에서

(2) 명령어

```
./gradlew dependencies
```

---

### **\[2\] 주요 라이브러리 살펴보기**

#### **메인 디펜던시, 큰 범주 3개**

**spring-boot-starter-data-jpa**

**spring-boot-starter-web**

**spring-boot-starter-thymeleaf**

**......**

**spring-boot-starter-web**

\- tomcat

\- webmvc 

\- json

**spring-boot-starter-data-jpa**

spring-core

aop

jdbc - HikariCP 물고옴

hibernate

spring-data-jpa

spring-boot-starter-logging

logback

slf4j 

**spring-boot-starter-thymeleaf**

#### **테스트 디펜던시**

**spring-boot-starter-test**

junit

mockito - mock 객체

assertj - 테스트를 좀 편하게

---

### **\[3\] 라이브러리 다시 정리**

#### **핵심 라이브러리**

스프링 MVC

스프링 ORM

JPA, 하이버네이트

스프링 데이터 JPA (스프링과 JPA를 알고 사용하는 응용기술)

#### **기타 라이브러리**

H2 데이터베이스 클라이언트

커넥션 풀 : 부트 기본은 HikariCP

타임리프 (WEB 관련)

로깅 SLF4J & LogBack

테스트

---

## **< 회차 : View 환경 설정 >**

start.spring.io에서 템플릿 엔진 종류들 나옴

스프링은 타임리프를 많이 지원함.

### **타임리프**

서버사이드 템플릿 엔진

#### **타임리프 특징들**

Natural Templates : 마크업을 깨지 않고, 그대로 그 안에서 사용한다.

따라서, 다른 엔진은 웹브라우저에서 안열리는데, 타임리프는 웹브라우저에서 열린다.

#### **단점**

메뉴얼 좀 보면서 사용해야한다는 어려움?

---

### **타임리프 템플릿 코드 샘플**

```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
</head>
<body>
<p th:text="'안녕하세요2 ' + ${data}" >안녕하세요. 손님</p>
</body>
</html>
```

**문서에 필요한 부분**

```
<html xmlns:th="http://www.thymeleaf.org">
```

**타임리프는 HTML 태그 안에서, 코드가 작성된다.**

```
<p th:text="'안녕하세요2 ' + ${data}" >안녕하세요. 손님</p>
```
