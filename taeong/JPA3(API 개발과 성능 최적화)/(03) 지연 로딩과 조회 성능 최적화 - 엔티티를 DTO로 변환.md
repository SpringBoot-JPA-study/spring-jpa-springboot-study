# 간단한 주문 조회 v2 : 엔티티를 DTO로 변환

```java
@GetMapping("/api/v2/simple-orders")
public List<SimpleOrderDto> orderV2() {
    List<Order> all = orderRepository.findAll(new OrderSearch());
    return all.stream()
            .map(SimpleOrderDto::new)
            .collect(toList());
}
```

Dto 클래스가 아래와 같이 정의되어있을 때, 

```java

@Data
static class SimpleOrderDto {
    private Long orderId;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;

    public SimpleOrderDto(Order order) {
		    orderId = order.getId();  //ORDER 테이블 조회
		    name = order.getMember().getName(); //MEMBER 테이블 조회
		    orderDate = order.getOrderDate();
		    orderStatus = order.getStatus();
		    address = order.getDelivery().getAddress();  //DELIVERY 테이블 조회
		}
}
```

예상대로라면 `ORDER → MEMBER → DELIVERY` 순서대로 테이블을 조회해야 함

그러나 실제로 출력되는 쿼리를 보면 `ORDER → MEMBER → DELIVERY → MEMBER → DELIVERY` …. 순서대로 출력됨

- ORDER 조회 쿼리 1번 → 반환된 행 N개라고 할 때,
- 최악의 경우
    - MEMBER * N번
        - 한 ORDER에 대해서 각각 다른 MEMBER 정보를 가지는 경우
        - 만약 2개 이상의 ORDER가 같은 MEMBER 정보를 가진다면 참조하는 갯수는 N개 미만이 됨
            - 지연로딩은 영속성 컨텍스트에서 조회하므로, 이미 조회된 경우 쿼리를 생략하기 때문
    - DELIVERY * N번
        - 한 ORDER에 대해서 각각 다른 DELIVERY 정보를 가지는 경우
    - 총 출력된 쿼리 수 = `1+N*(참조해야하는 외부 테이블 갯수)`

지연로딩으로 되어있는 프록시 하나하나를 초기화해주기 위해서 결과 수만큼 쿼리를 계속 날리는 것! ⇒ 성능 문제 발생

<BR>
  
### 그럼 EAGER 전략으로 바꾸면 문제가 해결되지 않을까?

EAGER로 바꿔서 실행해보면..

```sql
select
        order0_.order_id as order_id1_6_2_,
        order0_.delivery_id as delivery4_6_2_,
        order0_.member_id as member_i5_6_2_,
        order0_.order_date as order_da2_6_2_,
        order0_.status as status3_6_2_,
        delivery1_.delivery_id as delivery1_2_0_,
        delivery1_.city as city2_2_0_,
        delivery1_.street as street3_2_0_,
        delivery1_.zipcode as zipcode4_2_0_,
        delivery1_.status as status5_2_0_,
        member2_.member_id as member_i1_4_1_,
        member2_.city as city2_4_1_,
        member2_.street as street3_4_1_,
        member2_.zipcode as zipcode4_4_1_,
        member2_.name as name5_4_1_ 
    from
        orders order0_ 
    left outer join
        delivery delivery1_ 
            on order0_.delivery_id=delivery1_.delivery_id 
    left outer join
        member member2_ 
            on order0_.member_id=member2_.member_id 
    where
        order0_.delivery_id=?
```

쿼리가 와랄라 실행되면서 중간에 이런 복잡한 쿼리가 나간다…

- (쿼리 수가 줄어드는 것도 아니고 성능을 챙긴 것도 아닌…)
- 어떤 쿼리가 생성되는지 개발자가 예측할 수 없음

💡 주의: **지연 로딩(LAZY)을 피하기 위해 즉시 로딩(EARGR)으로 설정하면 안된다!** 즉시 로딩 때문에 연관관계가 필요 없는 경우에도 데이터를 항상 조회해서 **성능 문제가 발생**할 수 있다. **즉시 로딩으로 설정하면 성능 튜닝이 매우 어려워 진다.**

👍🏻 **항상 지연 로딩을 기본으로 하고, 성능 최적화가 필요한 경우에는 페치 조인(fetch join)을 사용하자**

