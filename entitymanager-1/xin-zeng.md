# 新增

EntityManager在進行新增時與Repository一樣，會先進行查詢判斷資料是否存在，再進行insert 或update，目前無法像Repository一樣處理此情況，所以建議直接寫SQL處理此狀況。

* persist -> select insert or update (不進行介紹)
* 自行寫insert SQL

```java
@PersistenceContext
private EntityManager em;

Query query = em.createNativeQuery("insert into tbl_order value(:orderID, :customerID, :salesID, :orderDate, :deliveryDate)");
query.setParameter("orderID", "");
query.setParameter("customerID", "");
query.setParameter("salesID", "");
query.setParameter("orderDate", "");
query.setParameter("deliveryDate", "");
query.executeUpdate();
```

