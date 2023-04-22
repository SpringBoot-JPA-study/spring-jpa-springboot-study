# 간단한 주문 조회 v4 : JPA에서 DTO로 바로 조회

- 일반적인 SQL을 사용할 때 처럼 원하는 값을 선택해서 조회
- new 명령어를 사용해서 JPQL의 결과를 DTO로 즉시 변환

```java
@Data
 public class OrderSimpleQueryDto {
    private Long orderId;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;

   public OrderSimpleQueryDto(Long orderId, String name, LocalDateTime orderDate, OrderStatus orderStatus, Address address) {
      this.orderId = orderId;
      this.name = name;
      this.orderDate = orderDate;
      this.orderStatus = orderStatus;
      this.address = address;
   }
}
```

```java
public List<OrderSimpleQueryDto> findOrderDtos() {
    return em.createQuery(
            "select new com.jpabook.jpashop.repository.OrderSimpleQueryDto(o.id,m.name, o.orderDate, o.status, d.address) from Order o" +
                    " join o.member m" +
                    " join o.delivery d", OrderSimpleQueryDto.class
    ).getResultList();
}
```

- new 패키지명..을 써야하는 부분이 지저분하긴 하지만… 아래의 결과를 확인해보면 딱 fit한 select문이 출력됨

```sql
select
    order0_.order_id as col_0_0_,
    member1_.name as col_1_0_,
    order0_.order_date as col_2_0_,
    order0_.status as col_3_0_,
    delivery2_.city as col_4_0_,
    delivery2_.street as col_4_1_,
    delivery2_.zipcode as col_4_2_ 
from
    orders order0_ 
inner join
    member member1_ 
        on order0_.member_id=member1_.member_id 
inner join
    delivery delivery2_ 
        on order0_.delivery_id=delivery2_.delivery_id
```

- **장점 ) SELECT 절에서 원하는 데이터를 직접 선택하므로 DB 애플리케이션 네트웍 용량 최적화**
    - `fetch join 최적화`에서 사용한 결과보다 쿼리 성능을 최적화할 수 있음!
    - 그치만 요즘 네트워크 성능이 너무 좋아서 생각보다 큰 차이가 나지 않음
- **단점1 ) 리포지토리 재사용성 떨어짐**
    - 엔티티 타입으로 반환되는 쿼리의 경우 모든 데이터를 불러온다음에 성능 튜닝
    - DTO타입으로 반환되는 쿼리는 해당하는 데이터만 불러오기 때문에 같은 DTO를 사용해야만 재사용 가능
- **단점2 ) API 스펙에 맞춘 코드가 리포지토리에 들어가는 단점**
    - 논리적으로 계층이 다 깨져있는 상태
    - 화면이 리포지토리에 의존하고 있는 상태.. API 스펙이 변경되면 리포지토리가 변경되어야 함
    - OCP 지켜지지 않음


<br>

### 쿼리 방식 선택 권장 순서

1. 우선 엔티티를 DTO로 변환하는 방법을 선택한다.
2. 필요하면 `페치 조인`으로 성능을 최적화 한다. 대부분의 성능 이슈가 해결된다. (대부분 성능 문제 해결)
3. 그래도 안되면 DTO로 직접 조회하는 방법을 사용한다.
4. 최후의 방법은 JPA가 제공하는 네이티브 SQL이나 스프링 JDBC Template을 사용해서 SQL을 직접 사용한다.
