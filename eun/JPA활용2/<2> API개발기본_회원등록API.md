# [JPA활용2]_API개발기본_회원등록API

<br>

---

# < 1. API의 중요성 >

요즘은 프론트 view단은 프론트 담당자가, 리액트나 vue같은 것으로 개발하고.

벤엔드 개발자는 API를 제공한다.

또한 JPA로 API를 개발하면, 그전과 다른 차원의 개발 노하우가 필요하다.

<br>

---

# < 2. 포스트맨 >

POST Method로 Json 데이터 요청으로 보내기

응답 출력도 Body에 Json으로 날라옴

<br>

---

# < 3. 컨트롤러 패키지 구분 >

(강의자 취향)

패키지 구분할 때,

컨트롤러의 경우

- 서버렌더링뷰를 담당하는 컨트롤러
- API인 경우의 컨트롤러

두가지를 구분함

<br>

---

# < 4. 컨트롤러 개발 MemberApiController.java >

## [ 1 ] API용 컨트롤러 클래스의 어노테이션

APIController만의 스프링 어노테이션

@RestController

:  @Controller + @ResponseBody 합친 거

<br>

## [ 2 ] 컨트롤러 설계 방향

### **방향1. 요청을 “엔티티”로 그대로 받는 것**

**→ 즉, V1엔티티를 Request Body에 직접 매핑 **

- 장점
    - 엔티티가 곧 스펙이 되기에 간편하다.
- 단점
    - API스펙 상 문제
        - 하나의 엔티티에는 다양한 API가 만들어지는데, 하나의 엔티티에 모든 API 요구사항을 담기는 어렵다.
        - 엔티티 변수가 변경되면, API스펙도 변경된다. → 즉 API를 사용하는 입장에서 곤란해진다.
    - 엔티티가 노출됬을 때 문제
        - 엔티티에 프레젠테이션 계층을 위한 로직이 추가된다.
        - 엔티티 자체에 API검증 로직도 들어가게된다.
    

### **방향2. 요청을 “DTO”로 받아서 넘기기**

**→ DTO를 RequestBody에 매핑**

- 단점
    - DTO클래스를 작성하고, 변수를 연결시켜야한다.
- 장점
    - 엔티티가 노출이 안된다.
        - 엔티티와 프레젠테이션 로직 분리가능
        - 엔티티와 API스펙 분리 가능 → DTO에 따라 다양한 API 가능
    - API스펙 일관성 유지
        - 개발 시, → 엔티티랑 스펙이 달라지면 에러가 뜬다.

### 결론

- 실무에서는 엔티티를 API스펙에 노출하면 안된다!!!


<br>


## [ 3 ] 방향1. 엔티티로 받기

path :   "/api/v1/members"

```java
@RestController
@RequiredArgsConstructor
public class MemberApiController {
	  private final MemberService memberService;
	
	  @PostMapping("/api/v1/members")
	  public CreateMemberResponse saveMemberV1(@RequestBody @Valid Member member){
	      Long id = memberService.join(member);
	      return new CreateMemberResponse(id);
	  }
	
		@Data
    static class CreateMemberResponse {
        private Long id;
        public CreateMemberResponse(Long id){
            this.id = id;
        }
    }

}
```

<br>


## [ 4 ] 방향2. DTO로 받기

path: path :   "/api/v2/members"

```java
@RestController
@RequiredArgsConstructor
public class MemberApiController {
    private final MemberService memberService;

	/*
    @PostMapping("/api/v1/members")
    public CreateMemberResponse saveMemberV1(@RequestBody @Valid Member member){
        Long id = memberService.join(member);
        return new CreateMemberResponse(id);
    }
	*/

    @PostMapping("/api/v2/members")
    public CreateMemberResponse saveMemberV2(@RequestBody @Valid CreateMemberRequest request){
        Member member = new Member();
        member.setName(request.getName());

        Long id = memberService.join(member);
        return new CreateMemberResponse(id);
    }
    @Data
    static class CreateMemberRequest {
				@NotEmpty
        private String name;

    }
    @Data
    static class CreateMemberResponse {
        private Long id;
        public CreateMemberResponse(Long id){
            this.id = id;

        }
    }

}
```

<br>



## [ 5 ] 기타 API스펙 검증

@NotEmpty

요청 입력값 존재 검증. 값이 없으면 안된다. 값이 없으면 에러를 띄움

……
