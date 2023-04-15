# < 1. yml 설정파일 수정 >

### ddl-auto변경

테이블을 자동으로 추가하거나 삭제할 일이 없으니까

ddl-auto: create → ddl-auto: none 으로 변경

</br>

---

# < 2. 컨트롤러 매핑 메서드 구현 >

## [ 방법1:  엔티티 노출 ]

```java
@GetMapping("/api/v1/members")
	 public List<Member> membersV1() {
	 return memberService.findMembers();
 }
```

### 엔티티 노출의 문제점

- 이전의 엔티티 노출 문제와 비슷하다.
    - 필요치 않은 정보까지 노출하게 된다.
    - 표현계층을 비즈니스계층에다가 구현하는 문제
    - API 스펙 수정의 문제
- 추가적으로 Json데이터의 최외층 형식이 배열로 전달된다.
    - 배열은 내부 배열요소가 동일하다. → 만약 추가적인 정보를 넣어야한다면, 배열요소가 되는 엔티티 자체가 수정되어야한다.
        - ex) 배열요소의 갯수(회원의 갯수)를 출력하려면, 엔티티 자체에 갯수변수를 넣어야한다.
    - 즉, Json형식 수정 확장이 어렵다.

- 추가 수업자료 파일에 있는 문제점
    
    조회 V1: 응답 값으로 엔티티를 직접 외부에 노출한
    문제점
    
    - 엔티티에 프레젠테이션 계층을 위한 로직이 추가된다.
    - 기본적으로 엔티티의 모든 값이 노출된다.
    - 응답 스펙을 맞추기 위해 로직이 추가된다. (@JsonIgnore, 별도의 뷰 로직 등등)
    - 실무에서는 같은 엔티티에 대해 API가 용도에 따라 다양하게 만들어지는데, 한 엔티티에 각각의 API를 위한 프레젠테이션 응답 로직을 담기는 어렵다.
    - 엔티티가 변경되면 API 스펙이 변한다.
    - 추가로 컬렉션을 직접 반환하면 항후 API 스펙을 변경하기 어렵다.(별도의 Result 클래스 생성으로
    
    **해결)**
    결론
    API 응답 스펙에 맞추어 별도의 DTO를 반환한다.
    

만약 Json의 외부층이 obeject로 되어있으면,

표현계층에서 추가적인 값을 Json에 추가하는 방식으로 구현가능하다.

```java
ex)

object로 하는 경우 count를 따로 추가 가능하다.
{
	"count": 2
	"member_data": [
		{
			"id": 1,
			"name::"test1",
			"address":{
				"city":null
				"street":null
				"zipcode":null
			}
		},
		{
			"id": 2,
			"name::"test3",
			"address":{
				"city":null
				"street":null
				"zipcode":null
			}
		}
	]
}
```

</br>

## [ 방법2:  별도의 DTO 사용 ]

```java
@GetMapping("/api/v1/members")
	 public List<Member> membersV1() {
	 return memberService.findMembers();
 }

@GetMapping("/api/v2/members")
	 public Result membersV2() {
	 List<Member> findMembers = memberService.findMembers();

	 //엔티티 -> DTO 변환
	 List<MemberDto> collect = findMembers.stream()
		 .map(m -> new MemberDto(m.getName()))
		 .collect(Collectors.toList());
	 return new Result(collect);
 }
```

### 관련 클래스 구현

```java
@Data
 @AllArgsConstructor
	 static class Result<T> {
	 private T data;
 }
 @Data
 @AllArgsConstructor
	 static class MemberDto {
	 private String name;
 }
```

### 특이사항

- 스펙에 노출되는 DTO  →  Result<T>
- Member의 일부정보만 담는 DTO →  MemberDto
- List<MemberDto> 로 만들어서 Result에다가 넣기

- 즉 3중구조

원본 Member엔티티 → **MemberDto** → **List<MemberDto> 리스트로 모으기** → **Result로 감싸기**

</br>
  
### [ 스펙 변경하는 경우 예시 ]

만약 회원수를 Json에 추가할 경우 → 최외층 Result에서 값을 추가해주면 된다.

```java

/* Result에 count 값 추가 */
@Data
 @AllArgsConstructor
	static class Result<T> {
		private int count;
		private T data;
 }

/* count 세기 */
	/* 걍 컬렉션 갯수 세서 반환하면 끝 */
@GetMapping("/api/v2/members")
	 public Result membersV2() {
	 List<Member> findMembers = memberService.findMembers();

	 //엔티티 -> DTO 변환
	 List<MemberDto> collect = findMembers.stream()
		 .map(m -> new MemberDto(m.getName()))
		 .collect(Collectors.toList());
		// collect 갯수 세기
	 return new Result(collect.size(), collect);
 }
```

</br>
  
---

# < 3. 기타  >

### 어노테이션

@ JasonIgnore

→ 해당 값을 Json에서 없애는 것. 표현시키지 않는 것.

……
