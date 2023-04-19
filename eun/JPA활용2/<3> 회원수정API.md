### API개발기본_회원수정API


---

# < 1. 회원수정과 PUT메서드 >

## PUT메서드

- HTTP메서드 중에서 PUT메서드
- 주로 CRUD중에서 Update기능
- 멱등성이 보장된다. (POST를 제외하고는 GET,PUT,DELETE 멱등성을 보장함)

- (기타) 멱등성
    
    동일한 요청을 여러번 보내도, 1번 보냈을 때와 동일한 성질. 즉 응답정보와 서버기록이 동일하다.
    
    POST는 멱등성을 보장안한다. 그래서 수정기능 자체만 할 때는 POST보다는 PUT을 쓰는게 좋다.
    
    
</br></br>

---

# < 2. 회원수정 API 구현 >

→ 이름만 수정한다.

</br>

## [ 1. request와 response 클래스 구현 ]

```java
	@Data
    static class UpdateMemberRequest{
        private String name;
    }

    @Data
    @AllArgsConstrutor
    static class UpdateMemberResponse{
        private Long id;
        private String name;
    }
```

### 특이사항

- Lombok 코딩스타일 (강사 성향)
    - 엔티티를 다룰 때는, Lombok을 제한적으로 사용한다.
    - 반면에 DTO는 어차피 데이터만 담기기 때문에, Lombok을 잘 쓴다.

</br>

## [  2. 컨트롤러 매핑 메서드 구현 ]

```java
/* <클래스>:  MemberAPIController

/* 회원수정API 매핑 메서드 */

//일단 별도의 등록과는 다른 수정만의 requst, response를 만들었음
    @PutMapping("/api/v2/members/{id}")
    public UpdateMemberResponse updateMemberV2(
            @PathVariable("id") Long id,
            @RequestBody @Valid UpdateMemberRequest request){

        //수정할 때, 가급적이면 JPA의 변경감지기능을 사용하자.
        memberService.update(id, request.getName());
    }
```

### 특이사항

- Put을 사용 → id 지정해주는 identifier사용
- PathVariable과 RequestBody, 2개를 동시 사용해서 받음
    - PathVariable로 path로 적은 id를 받음
    - RequestBody, request 객체로 name받음

</br>

## [ 3. 서비스 클래스에서, update메서드 구현 ]

```java
/* <클래스> :  MemberService.java

@Transactional
    public void update(Long id, String name) {
				//엔티티로 가져오기
        Member member = memberRepository.findOne(id);
				//나중에 변경감지
        member.setName(name);
    }
```

### 특이사항

- 특별한 특이사항 없음
    - id로 엔티티 찾아서 혹은 엔티티 만들음
    - setName으로 변경시킴
