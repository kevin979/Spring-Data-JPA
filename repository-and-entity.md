---
description: 資料表對接介面(Repository)與資料表實體類別(Entity)
---

# Repository & Entity



### Repository

Repository是用來定義與資料庫連面的介面，而Spring boot 實作後類別為 JpaRepository，所以建立介面實需繼承此類別。

<pre class="language-java" data-full-width="false"><code class="lang-java"><strong>@Repository
</strong>public interface AccountRepository extends JpaRepository&#x3C;Entity, idClass> {
}
</code></pre>

通常在建立介面是還會繼承另外一個類別 JpaSpecificationExecutor，此類別是Spring boot為了自定義查詢條件所建立的類別。

{% code fullWidth="false" %}
```java
@Repository
public interface AccountRepository extends JpaRepository<Entity, idClass>, JpaSpecificationExecutor<Entity> {
}
```
{% endcode %}

因為Spring Data JPA已將大部分的資料庫功能(CURD)實作完成，所以建立完介面後就可以直接使用了。

### Entity

Entity是用來定義資料表的欄位實體類，因此Entity與Repository是1對1的。

```java
@Getter
@Setter
@Entity
@Table(name = "tbl_order_detail", schema = "example")
@IdClass(value = OrderDetailEntityID.class) //此為定義ID
public class OrderDetailEntity implements Serializable {
    @Id
    @Column(name = "fld_order_id") //如果欄位名稱與變數名稱一致，可省略@Column定義
    private String orderID;
    @Id
    @Column(name = "fld_item_id")
    private Integer itemID;
    @Column(name = "fld_product_id")
    private String productID;
    @Column(name = "fld_count")
    private Integer count;
    @Column(name = "fld_unit_price")
    private Integer unitPrice;
    @Column(name = "fld_create_time")
    private LocalDateTime crTime;
    @Column(name = "fld_update_time")
    private LocalDateTime chTime;
}
```

JPA的Entity有一個非常特別的特性，當Table的PK是複合屬性(有兩個以上欄位是PK)時，需另外定義ID類別，否則JPA Entity會無法識別ID，進而導致出現錯誤。

<pre class="language-java"><code class="lang-java">@Getter
@Setter
public class OrderDetailEntityID implements Serializable {
    private String orderID;
<strong>    private Integer itemID;
</strong>}
</code></pre>
