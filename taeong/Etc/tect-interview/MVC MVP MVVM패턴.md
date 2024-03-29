# MVC MVP MVVM패턴

## MVC패턴

- 모델, 뷰, 컨트롤러로 이루어짐
- **모델** : 애플리케이션의 데이터 (데이터베이스, 상수, 변수 등)
- **뷰** : textarea, checkbox 등 UI 요소를 나타냄. 사용자가 볼 수 있는 화면
- **컨트롤러** : 하나 이상의 모델과 하나 이상의 뷰를 잇는 다리 역할. 메인 로직을 담당. 모델과 뷰의 생명주기 관리
- 장점
    - 애플리케이션의 구성 요소를 세 가지 역할로 구분하여 개발 프로세스에서 **각각의 구성 요소에만 집중해서 개발**할 수 있음
    - **재사용성과 확장성**이 용이
- 단점
    - 애플리케이션이 복잡해질수록 **모델과 뷰의 관계가 복잡해짐**

<br>

## MVP패턴

- Controller가 **Presenter로 교체된 패턴**
- **View와 Presenter는 1:1 관계**이므로 MVC보다 더 강한 결합을 지닌 디자인 패턴


<br>

## MVVM패턴

- Controller가 **View Model로 바뀐 패턴**
- View Model은 View를 추상화한 계층 (1:N 관계)

<br>

## 차이점
|  | MVC패턴 | MVP패턴 | MVVM패턴 |
| --- | --- | --- | --- |
| 관계 | 컨트롤러와 뷰는 1:N | 프레젠터와 뷰는 1:1 | 뷰모델과 뷰는 1:N |
| 참조 | 뷰는 컨트롤러를 참조X | 뷰는 프레젠터를 참조O | 뷰는 뷰모델을 참조O |
