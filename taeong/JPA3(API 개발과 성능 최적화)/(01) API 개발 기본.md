# API개발 기본

```java
public CreateMemberResponse saveMemberV1(@RequestBody @Valid Member member) { ... }
```

- @RequestBody : JSON으로 온 BODY를 Member 엔티티로 변경해서 정보를 넣어준다
- 파라미터로 @RequestBody 어노테이션 옆에 @Valid를 작성하면, RequestBody로 들어오는 객체에 대한 검증을 수행한다. 

```java
@PostMapping("/api/v1/members")
public CreateMemberResponse saveMemberV1(@RequestBody @Valid Member member) {
    Long id = memberService.join(member);
    return new CreateMemberResponse(id);
}
```

- 엔티티와 api 스펙이 1:1로 매핑되어 있는 상태 → api 스펙을 위한 별도의 dto를 만들어야 한다!

- api 파라미터를 절대 엔티티로 받아서는 안됨 + 엔티티를 외부에 노출해서도 안됨

```java
@PostMapping("/api/v2/members")
public CreateMemberResponse saveMemberV2(@RequestBody @Valid CreateMemberRequest request) {
    Member member = new Member();
    member.setName(request.getName());
    Long id = memberService.join(member);
    return new CreateMemberResponse(id);
}

@Data
static class CreateMemberRequest {
    private String name;
}
```
