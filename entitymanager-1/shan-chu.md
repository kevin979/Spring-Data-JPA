# 刪除

刪除的Method就只有remove可以使用，其擴充性較Repository差，使用時須完整查出資料後將Entity帶入。

```java
@PersistenceContext
private EntityManager em;
OrderEntity entity = em.find(OrderEntity.class, "P20240130001");
em.remove(entity);
```
