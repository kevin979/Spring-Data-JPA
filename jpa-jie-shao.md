---
description: 什麼是JPA、Spring Data JPA跟JPA哪裡不一樣？
---

# JPA介紹

### ORM - Object-Relational Mapping

介紹JPA之前要先介紹什麼是ORM，ORM表示物件關聯對映。在物件導向程式設計的軟體開發中，通過ORM，就可以把物件映射到關係型資料庫中。只要有一套程式能夠做到建立物件與資料庫的關聯，操作物件就可以直接操作資料庫數據，就可以說這套程式實現了ORM物件關聯對映。簡單的說：ORM就是建立實體物件和資料庫表之間的關係，從而達到操作實體物件就相當於操作資料庫表的目的。

當實現一個應用程式時（不使用O/R Mapping），我們可能會寫特別多數據訪問層的代碼，從數據庫保存數據、修改數據、刪除數據，而這些代碼都是重複的。而使用ORM則會大大減少重複性代碼。對象關係映射（Object Relational Mapping，簡稱ORM），主要實現程序對象到關係數據庫數據的映射。

### JPA - Java Persistence API

JPA是一個標準規範及接口(API)來實現ORM(object-relational mapping)框架，JPA通過註解(annotation) \[如：@Entity、@Table、@Column等註解] 或XML描述物件－關係表的對映關係，並將執行期的實體物件持久化到資料庫中。

<mark style="color:red;">**JPA不是一種框架，而是一個標準規範。而要使用JPA的功能就需要Provider來配合，而Provider就是各種ORM框架。**</mark>

### JPA的優勢

* 標準化\
  JPA 是 JCP 組織發佈的 Java EE 標準之一，因此任何聲稱符合 JPA 標準的框架都遵循同樣的架構，提供相同的訪問API，這保證了基於JPA開發的企業應用能夠經過少量的修改就能夠在不同的JPA框架下運行。
* 容器級特性的支持\
  JPA框架中支持大數據集、事務、併發等容器級事務，這使得 JPA 超越了簡單持久化框架的局限，在企業應用發揮更大的作用。
* 簡單方便\
  JPA的主要目標之一就是提供更加簡單的編程模型：在JPA框架下創建實體和創建Java 類一樣簡單，沒有任何限制，只需要使用 javax.persistence.Entity進行註釋，JPA的框架和接口也都非常簡單，沒有太多特別的規則和設計模式的要求，開發者可以很容易的掌握。JPA基於非侵入式原則設計，因此可以很容易的和其它框架或者容器集成。
* 查詢能力\
  JPA的查詢語言是面向對象而非面向數據庫的，它以面向對象的自然語法構造查詢語句，可以看成是Hibernate HQL的等價物。JPA定義了獨特的JPQL（Java Persistence Query Language），JPQL是EJB QL的一種擴展，它是針對實體的一種查詢語言，操作對象是實體，而不是關係數據庫的表，而且能夠支持批量更新和修改、JOIN、GROUP BY、HAVING 等通常只有 SQL 才能夠提供的高級查詢特性，甚至還能夠支持子查詢。
* 高級特性\
  JPA 中能夠支持面向對象的高級特性，如類之間的繼承、多態和類之間的複雜關係，這樣的支持能夠讓開發者最大限度的使用面向對象的模型設計企業應用，而不需要自行處理這些特性在關係數據庫的持久化。



### Spring Data JPA

誠如上述所言，「JPA」 只是一種規範，並不是一種框架，而Spring Data JPA則是 Spring boot 實現JPA規範的「框架」，依據JPA的規範實作了CRUD、分頁、排序等等功能。



以上資料來源出處 「[JPA是什麼](https://ithelp.ithome.com.tw/articles/10229808)」

