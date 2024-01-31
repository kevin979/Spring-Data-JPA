# 新增

在Spring Data JPA中，沒有單獨進行 「新增」 的Method，常用的Method為 「save」、「saveAll」、「saveAndFlush」、「saveAllAndFlush」 這四個，「saveAll」 的參數是List\<Entity>，底層是使用迴圈呼叫「save」，另外 「save」、「saveAndFlush」 此兩者差異後續再說明，先說明 「save」 的功能。

save顧名思義就是儲存的意思，如果將執行SQL印出可以發現save的執行過程中會先進行查詢(select)的SQL後，再依據是否有值進行新增(insert)或更新(update)的判斷，因此在原先的Repository中是沒有新增的功能，因此新增會有3種方式可以進行操作。

* 使用上述所說的save 進行新增/更新資料

```java
@Autowired
private OrderDetailRepository repository;
OrderDetailEntity entity = new OrderDetailEntity();
entity.setOrderID("P20240130001");
entity.setItemID(1);
entity.setProductID("C0001");
entity.setCount(5);
entity.setUnitPrice(900);
repository.save(entity);
```

* 自行寫SQL進行資料新增，SQL一樣可以使用原生SQL或JPQL，不過此種寫法一但欄位過多時就很麻煩，變數會太多，且SQL也不好寫，因此不太建議使用此方式。另外，如果要使用SQL，則呼叫此方法需要使用@Transactional，否則會報錯。

```java
@Repository
public interface OrderRepository extends JpaRepository<OrderEntity, String>, JpaSpecificationExecutor<OrderEntity> {
    @Modifying
    @Query(value = "insert into tbl_order values(?1,?2,?3,?4,?5,'',?6,now(),now())", nativeQuery = true)
    void addOrder(String orderID, String customerID, String salesID, String orderDate, String deliveryDate, BigDecimal orderAmount);
}
```

* 最後一個方法則是在Entity中加工，讓Repository進行save時只做insert或update，不進入select，首先調整Entity的程式，讓其實作Persistable\<ID>，並預設物件為新物件。

```java
@Entity
@Table(name = "tbl_order")
public class OrderEntity implements Serializable, Persistable<String> {
    @Id
    @Column(name = "fld_order_id", nullable = false)
    private String orderID;
    @Column(name = "fld_customer_id", nullable = false)
    private String customerID;
    @Column(name = "fld_sales_id", nullable = false)
    private String salesID;
    @Column(name = "fld_order_date", nullable = false)
    private String orderDate;
    @Column(name = "fld_delivery_date", nullable = false)
    private String deliveryDate;
    @Column(name = "fld_real_delivery_date", nullable = false)
    private String realDeliveryDate;
    @Column(name = "fld_order_amount", nullable = false)
    private BigDecimal orderAmount;
    @Column(name = "fld_create_time", nullable = false, updatable = false)
    private LocalDateTime createTime = LocalDateTime.now();
    @Column(name = "fld_change_time", nullable = false)
    private LocalDateTime changeTime = LocalDateTime.now();
    @Transient //因為Entity會與資料表欄位進行Mapping，但資料表無此欄位，所以需忽略
    private boolean isNew = true;

    @Override
    public String getId() {
        return orderID;
    }

    @Override
    public boolean isNew() {
        return isNew;
    }
}
```

此時在建立Entity物件時，就會預設該物件是新資料，Repository在操作save時就不會進入select，而是直接使用insert，此時如有重複資料就會拋出主鍵重複錯誤(could not execute statement; SQL \[n/a]; constraint \[PRIMARY]; nested exception is org.hibernate.exception.ConstraintViolationException: could not execute statement)了。

一但加上Persistable後，在資料操作上就必須很明確的知道該資料是屬於insert或update，當isNew = true時表示新增(insert)，isNew = false時表示更新(update)。



### 附註說明

save 與 saveAndFlush的差異在於saveAndFlush會直接進行資料commit，而save並不是即時的，原因是因為JPA的操作特性在於資料的操作都是在 「物件」，因此在物件與資料庫的session中斷前，會依據當前資料量隨時進行commit，所以在操作JPA時要注意 <mark style="color:red;">**「不要隨意更動Entity的內容值，否則很可能會被寫入資料庫中」**</mark> 。
