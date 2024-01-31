# EntityManager

如果是使用EntityManager要進行分頁操作，則必須自行撰寫SQL，同樣的也是使用PageRequest進行分頁操作。

```java
@PersistenceContext
private EntityManager em;
	
PageRequest pageRequest = PageRequest.of(0, 20);
String countSQL = "select count(1) from tbl_order";
String selectSQL = "select * from tbl_order";
Query count = em.createNativeQuery(countSQL);
BigInteger totalCount = (BigInteger) count.getSingleResult();
Query query = em.createNativeQuery(selectSQL, OrderEntity.class);
query.setFirstResult(BigDecimal.valueOf(pageRequest.getOffset()).intValue());
query.setMaxResults(pageRequest.getPageSize());
List<OrderEntity> entities = query.getResultList();
Page<OrderEntity> page = new PageImpl<>(entities, pageRequest, totalCount.longValue());
```

此方式較之Repository來說比較麻煩，要自己寫SQL以及自己組合Page的內容，但遇到需要Join、Union之類多Table組合時彈性較足。
