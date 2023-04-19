# < 1. 샘플 입력할 내용 >

 API 개발 고급 설명을 위해 샘플 데이터를 입력하자.

유저2명이 각 1번의 오더, 오더1개의 상품 2개

userA
   JPA1 BOOK
   JPA2 BOOK
userB
   SPRING1 BOOK
   SPRING2 BOOK

</br>

---

# < 2. JPA 설정파일 수정 >

application.yml 설정파일

jpa hibernate의 ddl-auto: 옵션을 다시 create로 바꾸기

→ 앱 실행시점에, 테이블들을 드랍하고 초기화해준 코드대로 다시 만듬

</br>

---

# < 3. 샘플 입력 클래스 및 메서드 구현 >

## [ 호출용 메서드 ]

```java
@Component
@RequiredArgsConstructor
public class InitDb {
		/* 초기화 클래스 */
		private final InitService initService;
	
		/* 메서드 호출 */
		@PostConstruct
		public void init() {
				 initService.dbInit1();
				 initService.dbInit2();
		}
}
```

</br>

## [ 각 엔티티 초기화 재사용 메서드 ]

→ 초기화 클래스 내부의 메서드

```java
/* Member */
private Member createMember(String name, String city, String street,
String zipcode) {
	 Member member = new Member();
	 member.setName(name);
	 member.setAddress(new Address(city, street, zipcode));
	 return member;
 }

/* Book */
private Book createBook(String name, int price, int stockQuantity) {
	 Book book = new Book();
	 book.setName(name);
	 book.setPrice(price);
	 book.setStockQuantity(stockQuantity);
	 return book;
 }

/* Delivery */
 private Delivery createDelivery(Member member) {
	 Delivery delivery = new Delivery();
	 delivery.setAddress(member.getAddress());
	 return delivery;
 }
 
```

</br>

## [ 초기화기능 클래스 :  InitService ]

**구조**

- 각 유저별 샘플데이터 생성 메서드 2개
- 생성용 메서드가 사용하는, 엔티티 초기화 재사용 메서드

```java
	@Component
	@Transactional
	@RequiredArgsConstructor
	static class InitService {
		private final EntityManager em;
	
		/* 사용자1 */
		public void dbInit1() {
				Member member = createMember("userA", "서울", "1", "1111");
				em.persist(member);
		
				Book book1 = createBook("JPA1 BOOK", 10000, 100);
				em.persist(book1);
		
				Book book2 = createBook("JPA2 BOOK", 20000, 100);
				em.persist(book2);
		
				OrderItem orderItem1 = OrderItem.createOrderItem(book1, 10000, 1);
				OrderItem orderItem2 = OrderItem.createOrderItem(book2, 20000, 2);
		
				Delivery delivery = createDelivery(member);
	
				Order order = Order.createOrder(member, delivery, orderItem1, orderItem2);
				em.persist(order);
		}

		/* 사용자2 입력 */
		public void dbInit2() {
				Member member = createMember("userB", "진주", "2", "2222");
				em.persist(member);
				
				Book book1 = createBook("SPRING1 BOOK", 20000, 200);
				em.persist(book1);
				
				Book book2 = createBook("SPRING2 BOOK", 40000, 300);
				em.persist(book2);
				
				OrderItem orderItem1 = OrderItem.createOrderItem(book1, 20000, 3);
				OrderItem orderItem2 = OrderItem.createOrderItem(book2, 40000, 4);
				
				Delivery delivery = createDelivery(member);
				
				Order order = Order.createOrder(member, delivery, orderItem1,
				orderItem2);
				em.persist(order);
		}
 }
```

</br>

---

# < 추가내용  >

## 추가 어노테이션

### **@PostConstruct**

**지금 사용목적** 

:   앱 로딩 시점에, 샘플데이터 생성 메서드를 호출하고 싶어서.

**팁**

- 스프링 라이프 라이클 때문에, PostConstruct먹인 메서드에다가 코드 넣으면 잘 안된다.
- 따라서 구체적 코드는 다른 메서드에서 구현하고, PostConstruct먹힌 메서드가 구현메서드를 호출하는 방향으로 한다.

**PostContruct**

- 종속성 주입이 완료된 후 실행되어야 하는 메서드에 사용된다.
- 이 어노테이션은 다른 리소스에서 호출되지 않아도 수행된다.
- 따라서 초기화 목적의 메서드에 사용된다.
    - 생성자가 호출되었을 때, 빈은 초기화되지 않았음 (의존성 주입이 이루어지지 않았음)
    - PostConstruct는 의존성주입이 끝나고 실행됨을 보장
    - 따라서 초기화가 됨을 보장
    - bean의 생애주기에서 오직 한 번만 수행됨을 보장 (앱 실행 시점에 한번만 실행됨)

## Extract Method 사용해보기

중복되는 부분을 메서드로 만들어서 해결

……
