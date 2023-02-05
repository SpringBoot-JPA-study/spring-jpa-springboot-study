# 영속성 전이(CASCADE)와 고아 객체

## 영속성 전이 : CASCADE

- 특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만들고 싶을 때
- 연관관계 매핑이랑 전혀 관련 없음
- 엔티티를 영속화할 때 연관된 엔티티도 함께 영속화하는 편리함을 제공할 뿐
- ex. 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장하는 경우에 사용
    - 1개의 부모 엔티티와 2개의 자식 엔티티를 영속화하려면?
        
        ```java
        public class Parent{
        	...
        	@OneToMany(mappedBy="parent")
        	private List<Child> childList = new ArrayList<>();
        	
        	public void addChild(Child child){
        		childList.add(child);
        		child.setParent(this);
        	}
        }
        ```
        
        ```java
        Parent parent = new Parent();
        Child child1 = new Child();
        Child child2 = new Child();
        
        parent.addChild(child1);
        parent.addChild(child2);
        
        em.persist(parent);
        em.persist(child1);
        em.persist(child2);
        ```
        
        - 일일이 자식 엔티티까지 영속화 작업을 수행하기에는 너무 번거롭다!
    - 부모에 cascade 속성을 지정해줘서 자식 엔티티가 자동으로 영속화되도록 함
        
        ```java
        public class Parent{
        	...
        	@OneToMany(mappedBy="parent", cascade=CascadeType.All)
        	private List<Child> childList = new ArrayList<>();
        	...
        }
        ```
        
        - `em.persist(parent)`만 해도 자식 엔티티까지 모두 영속화시킴


<br>

- cascade는 자식이 부모에만 연관된 엔티티일 때 사용하기!
    - 사용가능한 경우
        - 단일 엔티티에 완전히 종속적일 때
        - 부모-자식의 라이프사이클이 완전히 동일할 때
        - 소유자가 유일할 때

<br>

## 고아객체

- 고아 객체 제거 : 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제하는 것
- 참조가 제거된 엔티티는 다른 곳에서 참조하지 않는 고아 객체로 인식함 → 자동 삭제
- `orphanRemoval = true` 속성 지정
    
    ```java
    public class Parent{
    	...
    	@OneToMany(mappedBy="parent", orphanRemoval = true)
    	private List<Child> childList = new ArrayList<>();
    	...
    }
    ```
    
    ```java
    Parent findParent = em.find(Parent.class, parent.get());
    findParent.getChildList().remove(0);
    ```
    
    - 컬렉션에서 0번 자식엔티티가 remove되면 JPA에서 자동으로 데이터베이스에 DELETE쿼리 전송
- cascade와 동일하게 특정 엔티티가 개인 소유할 때, 참조하는 곳이 하나일 때 사용해야 함
- cascade옵션이 없더라도 `orphanRemoval = true`로 설정해두면 부모 엔티티를 삭제하는 경우 자식 컬렉션까지 모두 삭제
    - `CascadeType.REMOVE`와 동일하게 동작
- @OneToOne, @OneToMany만 가능

<br>

## cascade랑 orphanRemoval 같이 쓰기

- 스스로 생명주기를 관리하는 엔티티는 em.persist()로 영속화, em.remove()로 제거 (JPA의 영속성 컨텍스트를 통해)
- 두 옵션을 모두 활성화하면 부모 엔티티를 통해서 자식의 생명주기를 관리할 수 있음
    
    ```java
    public class Parent{
    	...
    	@OneToMany(mappedBy="parent", cascade=CascadeType.All, orphanRemoval = true)
    	private List<Child> childList = new ArrayList<>();
    	...
    }
    ```
    
- 도메인 주도 설계의 [Aggregate root 개념](https://eocoding.tistory.com/36#:~:text=Aggregate%20Root%EC%99%80%20%EC%A0%84%EC%97%AD%20%EC%8B%9D%EB%B3%84%EC%9E%90)을 구현할 때 유용
    - 어그리거트 : 도메인을 어떻게 묶느냐에 대한 기준, 연관된 도메인들을 묶은 집합
