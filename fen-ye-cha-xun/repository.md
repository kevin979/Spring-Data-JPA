# Repository

傳統在進行分頁操作時，需撰寫分頁SQL，而不同資料庫的分頁SQL又不盡相同，而Spring data JPA有協助實作此功能。

使用PageRequest，就可以輕鬆達到分頁效果。

```java
@Autowired
private OrderRepository repository;
	
PageRequest pageRequest = PageRequest.of(0, 20, Sort.by(Sort.Order.asc("createTime"), Sort.Order.desc("orderID")));
Page<OrderEntity> page = repository.findAll(pageRequest);
List<OrderEntity> orderEntityList = page.getContent();
```

* PageRequest.of(查詢頁數, 單頁筆數, 排序)
* Page\<Entity> 查詢後的資料是使用Page包裝
  * page.getTotalPages() -> 取得總頁數
  * page.getTotalElements() -> 取得資料總數
  * page.getContent() -> 取得當前內容
* 務必切記頁數是從0開始計算

同時也可以配合之前Repository-查詢的內容所提到的specification進行條件設置

```java
PageRequest pageRequest = PageRequest.of(0, 20, Sort.by(Sort.Order.asc("createTime"), Sort.Order.desc("orderID")));
Specification<OrderEntity> specification = ((root, query, criteriaBuilder) -> {
    List<Predicate> criteriaList = new ArrayList<>();
    criteriaList.add(criteriaBuilder.equal(root.get("customerID"), "A001"));
    return criteriaBuilder.and(criteriaList.toArray(new Predicate[0]));
});
Page<OrderEntity> page = repository.findAll(specification, pageRequest);
List<OrderEntity> entities = page.getContent();
```
