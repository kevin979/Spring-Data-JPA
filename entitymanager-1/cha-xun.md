# 查詢

使用EntityManager進行查詢有兩種方式，一種是使用Spring Data JPA建置好的find()，另外一種就是自行建置SQL，而此處自行建置SQL有更多種方式可以進行，可採用原生SQL、JPQL之外，可以在Entity中建置JPQL並給予別名。

* find(Entity.class, PK)

```java
@PersistenceContext
private EntityManager em;
OrderEntity entity = em.find(OrderEntity.class, "P20240130001");
```

* 自行建立SQL

```java
@PersistenceContext
private EntityManager em;

//使用原生SQL
Query query = em.createNativeQuery("select * from tbl_order where fld_order_id = :id", OrderEntity.class);
query.setParameter("id", "P20240130001");
OrderEntity entity = (OrderEntity) query.getSingleResult(); //查詢單一資料
List<OrderEntity> entityList = query.getResultList(); //查詢多筆資料

//使用JPQL
Query query = em.createQuery("select a from OrderEntity a where a.orderID = :id");
query.setParameter("id", "P20240130001");
OrderEntity entity = (OrderEntity) query.getSingleResult(); //查詢單一資料
List<OrderEntity> entityList = query.getResultList(); //查詢多筆資料
```

* 如果要使用JPQL別名，則須要先調整Entity，先在Entity中建立好SQL並給予別名後才能使用，<mark style="color:red;">**別名在專案內必須是唯一的**</mark>。

```java
@NamedQuery(name = "QueryBySalesID", query = "select a from OrderEntity a where a.salesID = :salesID")
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
    private String realDeliveryDate = "";
    @Column(name = "fld_order_amount", nullable = false)
    private BigDecimal orderAmount;
    @Column(name = "fld_create_time", nullable = false, updatable = false)
    private LocalDateTime createTime = LocalDateTime.now();
    @Column(name = "fld_change_time", nullable = false)
    private LocalDateTime changeTime = LocalDateTime.now();
    @Transient
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

```java
@PersistenceContext
private EntityManager em;

//使用JPQL別名
Query query = em.createNamedQuery("QueryBySalesID");
query.setParameter("salesID", "S009");
OrderEntity entity = (OrderEntity) query.getSingleResult(); //查詢單一資料
List<OrderEntity> entityList = query.getResultList(); //查詢多筆資料
```

如果需要多個別名JPQL，則可以使用NamedQueries

```java
@NamedQueries({
        @NamedQuery(name = "QueryBySalesID", query = "select a from OrderEntity a where a.salesID = :salesID"),
        @NamedQuery(name = "QueryByCustomerID", query = "select a from OrderEntity a where a.customerID = :customerID")
})
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
    private String realDeliveryDate = "";
    @Column(name = "fld_order_amount", nullable = false)
    private BigDecimal orderAmount;
    @Column(name = "fld_create_time", nullable = false, updatable = false)
    private LocalDateTime createTime = LocalDateTime.now();
    @Column(name = "fld_change_time", nullable = false)
    private LocalDateTime changeTime = LocalDateTime.now();
    @Transient
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

