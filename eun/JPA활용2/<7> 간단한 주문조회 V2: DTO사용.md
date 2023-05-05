# < 간단한 주문조회V2: 엔티티를 DTO로 변환 >

## [ 1.구현하기 ]

### {  V2용 매핑 메서드 }

```java
// 원래는 List를 Result로 감싸는 것으로 배웠지만, 일단 학습을 위해 생략
@GetMapping("/api/v2/simple-orders")
public List<SimpleOrderDto> ordersV2() {
	List<Order> orders = orderRepository.findAll();
	List<SimpleOrderDto> result = orders.stream()
		.map(o -> new SimpleOrderDto(o))
		.collect(toList());

	return result;
}

```

</br>

### { V2용 DTO 클래스 만들기 }

```java
//V2용 DTO
	/*
		static class로 만들기
	*/

static class SimpleOrderDto{
	private Long orderId;
	private String name;
	private LocalDateTime orderDate; //주문시간
	private OrderStatus orderStatus;
	private Address address;
	
	public SimpleOrderDto(Order order) {
		/* 
			(기타) Dto가 Order엔티티를 받는 건, 괜찮다. 
			엔티티가 중요하다지만 엔티티 자체를 노출하는게 아니니까
		*/
		orderId = order.getId();
		name = order.getMember().getName(); //Lazy 초기화
		orderDate = order.getOrderDate();
		orderStatus = order.getStatus();
		address = order.getDelivery().getAddress();  //Lazy 초기화
	}	
}
```

</br>

## [ 2. 한계점 ]

Lazy 조회에서 1+N 문제가 생긴다.

</br>

처음 요청에 1번의 쿼리에다가, 연결되어 조회할 엔티티에 따라 추가적인 N이 생길 수 있다.

만약 조회결과가 N개이고, 연결되어 조회할 엔티티가 1개라면외
최악의 경우,  1+N번 쿼리가 나간다.

</br>

만약 연결된 엔티티가 더 있다면 있는 만큼 +N이 늘어난다.

</br>

위의 V2에서는
member와 delivery를 2개가 있기에 N도 2번 추가된다.
```
쿼리가 총 1 + N + N번 실행된다. (v1과 쿼리수 결과는 같다.)
  order 조회 1번(order 조회 결과 수가 N이 된다.)
  order -> member 지연 로딩 조회 N 번
  order -> delivery 지연 로딩 조회 N 번
  
  예) order의 결과가 4개면 최악의 경우 1 + 4 + 4번 실행된다.(최악의 경우)
    지연로딩은 영속성 컨텍스트에서 조회하므로, 이미 조회된 경우 쿼리를 생략한다.
    간단한 주문 조회 V3: 엔티티를 DTO로 변환 - 페치 조인 최적
```

즉, 조회결과가 N개라면 → 쿼리가 1+N+N번 나간다.

</br>

생각해보자
만약 조회결과가 25개이고,  연결되어 조회할 엔티티가 4개라면???
요청 한번에 1+25+25+25 쿼리가 나간다!!!  101번 쿼리가 나가는 성능문제가 생긴다.

</br>

## [ 3. 해결방법 ]

→ 페치 조인을 사용한다. →  8강
