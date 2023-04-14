# 간단한 주문 조회 v3 : 엔티티를 DTO로 변환 - 페치 조인 최적화

JPQL에 `join fetch` 키워드를 사용하여 페치 조인을 적용할 수 있다.

```java
public List<Order> findAllWithMemberDeilvery() {
return em.createQuery(
        "select o from Order o" +
                " join fetch o.member m" +
                " join fetch o.delivery d", Order.class
).getResultList();
```

단 한번의 쿼리로 해결 가능 -> 거의 대부분의 성능 문제를 해결할 수 있음

```sql
select
    order0_.order_id as order_id1_6_0_,
    member1_.member_id as member_i1_4_1_,
    delivery2_.delivery_id as delivery1_2_2_,
    order0_.delivery_id as delivery4_6_0_,
    order0_.member_id as member_i5_6_0_,
    order0_.order_date as order_da2_6_0_,
    order0_.status as status3_6_0_,
    member1_.city as city2_4_1_,
    member1_.street as street3_4_1_,
    member1_.zipcode as zipcode4_4_1_,
    member1_.name as name5_4_1_,
    delivery2_.city as city2_2_2_,
    delivery2_.street as street3_2_2_,
    delivery2_.zipcode as zipcode4_2_2_,
    delivery2_.status as status5_2_2_ 
from
    orders order0_ 
inner join
    member member1_ 
        on order0_.member_id=member1_.member_id 
inner join
    delivery delivery2_ 
        on order0_.delivery_id=delivery2_.delivery_id
```

- 엔티티를 페치 조인(fetch join)을 사용해서 쿼리 1번에 조회
- 페치 조인으로 order -> member , order -> delivery 는 이미 조회 된 상태 이므로 지연로딩이 일어나지 않음! (아예 무시됨)
