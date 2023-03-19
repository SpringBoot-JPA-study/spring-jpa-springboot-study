# @ResponseBody의 동작 과정

- `@ResponseBody` : HTTP의 BODY에 문자 내용을 직접 반환
- viewResolver 대신에 `HttpMessageConverter` 가 동작
    - viewResolver는 템플릿 엔진에 데이터를 반환해줄 때 적절한 html파일로 랜더링하는 역할
- 기본 문자처리: `StringHttpMessageConverter`
    - 문자열로 데이터를 반환
- 기본 객체처리: `MappingJackson2HttpMessageConverter`
    - json형식으로 데이터를 반환
    - `Jackson2` : 객체를 json으로 변환하는 라이브러리(gson도 유명하지만 스프링은 jackson을 선택)
- byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음
