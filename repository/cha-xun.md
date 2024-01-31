# 查詢

在前面的Repostitory中提到Spring Data JPA有實作一支類別JpaRepository，此類別有時做了基本的資料庫操作Method，再來就是如何利用Repostitory進行查詢。

```java
// 查詢全部
@Autowired
private OrderDetailRepository repository;
List<OrderDetailEntity> entities = repository.findAll();

// 查詢全部並排序，其中排序的名稱是使用"實體類的變數名"，不是使用"欄位名稱"
List<OrderDetailEntity> entities = repository.findAll(Sort.by("orderID").and(Sort.by("itemID").descending()));

// 依據ID進行查詢
OrderDetailEntityID id = new OrderDetailEntityID();
id.setOrderID("123");
id.setItemID(1);
OrderDetailEntity entity = repository.findById(id);
```

因為Spring Data JPA僅實作了基本的查詢功能，若需要其他查詢條件，有3種方式可以進行操作

* 在定義Repostitory時會需要同時引入相對應的Entity，所以可以針對Entity內所定義的變數名稱進行查詢條件設置，在進行Method定義時IDE會協助，因此請跟著IDE的提示操作，避免命名錯誤導致SQL錯誤。

```java
@Repository
public interface OrderDetailRepository extends JpaRepository<OrderDetailEntity, OrderDetailEntityID>, JpaSpecificationExecutor<OrderDetailEntity> {
    List<OrderDetailEntity> findAllByOrderIDOrderByItemIDDesc(String orderID);
}
```

* 若是想要自定義SQL也可以，但自定義SQL有兩種方式可以進行操作，一種是使用SQL、另一種則是使用JPA獨有的JPQL(Java Persistence Query Language)，而引數的使用也有兩種方式。

```java
@Repository
public interface OrderDetailRepository extends JpaRepository<OrderDetailEntity, OrderDetailEntityID>, JpaSpecificationExecutor<OrderDetailEntity> {
    //使用JPQL
    @Query(value="select * from OrderDetailEntity a where a.crTime >= ?1")
    List<OrderDetailEntity> findAllByCrTime(LocalDateTime crTime);
    
    //使用JPQL + 引數採用別名
    @Query(value="select * from OrderDetailEntity a where a.crTime >= :crTime")
    List<OrderDetailEntity> findAllByCrTime(@Param("crTime") LocalDateTime crTime);

    //使用SQL
    @Query(value="select * from tbl_order_detail a where a.crTime >= ?1", nativeQuery = true)
    List<OrderDetailEntity> findAllByCrTime(LocalDateTime crTime);
    
    //使用SQL + 引數採用別名
    @Query(value="select * from tbl_order_detail a where a.crTime >= :crTime", nativeQuery = true)
    List<OrderDetailEntity> findAllByCrTime(@Param("crTime") LocalDateTime crTime);
}
```

* 最後一種方式是如果真的不想寫SQL且Method欄位過多又覺得寫死MethodName不夠彈性的解決方式，那就是使用Specification\<Entity>，如果需要排序，也可加入Sort，如同第一個範例一般。

```java
@Autowired
private OrderDetailRepository repository;

Specification<OrderDetailEntity> specification = (root, query, criteriaBuilder) -> {
    List<Predicate> criteriaList = new ArrayList<>();
    criteriaList.add(criteriaBuilder.between(root.get("crTime"), time1, time2));
    criteriaList.add(criteriaBuilder.and(root.get("orderID").in(orderIDs)));
    return criteriaBuilder.and(criteriaList.toArray(new Predicate[0]));
};

List<OrderDetailEntity> entities = repository.findAll(specification);
List<OrderDetailEntity> entities = repository.findAll(specification, Sort.by("orderID").and(Sort.by("itemID").descending()));
```
