# XML

- **eXtensible Markup Language**
    - 마크업언어(HTML)가 아니라 마크업언어를 정의(확장)하기 위한 언어
- 열린태그와 닫힌태그로 이루어진 구조의 데이터
- xml 옆에 version과 encoding을 씀 (=프롤로그)
- 최상위태그는 하나만 사용이 가능함
- **JSON vs XML**
    - XML은 열린-닫힌 태그가 있기 때문에 JSON보다 무거움
    - JavaScript Object로 변환하기 위해서 JSON보다는 더 많은 노력이 필요함
- **HTML vs XML**
    - HTML의 태그는 이미 약속한 태그들만 사용 가능
    - XML 태그는 사용자임의로 만들 수 있음
- 대표적인 사용 사례 : sitemap.xml
    - sitemap.xml : [SEO](https://library.gabia.com/contents/domain/4359/) 를 위한 설정 파일
        - SEO : 검색엔진이 이해하기 쉽도록 홈페이지의 구조와 페이지를 개발해 검색 결과 상위에 노출될 수 있도록 하는 작업
        - 포털의 SEO 동작방식
            - 크롤링봇이 사이트를 찾아다니면서 해당 사이트들의 정보를 수집 및 가공 → DB 안에 INSERT
            - 사용자가 검색하는 경우 이 DB를 기반으로 해서 검색 결과를 보여줌
        - 매우 큰 사이트의 경우 웹 크롤러가 신규 혹은 최근 업데이트된 페이지를 지나칠 우려가 있음 + 서로 링크가 종속적으로 연결되지 않은 경우 일부 페이지를 누락하는 일이 발생할 수 있음
        ⇒ 이를 sitemap.xml이 방지함
