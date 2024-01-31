# 設定資料庫連線

使用Spring boot 時，可在yml中進行設定

<pre class="language-yaml" data-full-width="false"><code class="lang-yaml"><strong>//連線地址 
</strong><strong>spring.datasource.url
</strong>
//帳號
spring.datasource.username

//密碼
spring.datasource.password

//驅動
spring.datasource.driver-class-name

//jpa啟動時，針對資料庫所執行動作
spring.jpa.hibernate.ddl-auto

//是否在控制檯顯示資料庫語句
spring.jpa.show-sql
</code></pre>

其中 「spring.jpa.hibernate.ddl-auto」 此項設定較為特殊，該設定是針對tomcat啟動時，JPA進行資料庫連接時所進行的操作，其中常見設定如下：

* create：每次載入hibernate時都會刪除上一次的生成的表，然後根據你的model類再重新來生成新表，哪怕兩次沒有任何改變也要這樣執行，這就是導致資料庫表資料丟失的一個重要原因。
* create-drop：每次載入hibernate時根據model類生成表，但是sessionFactory一關閉，表就自動刪除。
* update：第一次載入hibernate時根據model類會自動建立起表的結構（前提是先建立好資料庫），以後載入hibernate時根據model類自動更新表結構，即使表結構改變了但表中的行仍然存在不會刪除以前的行。要注意的是當部署到伺服器後，表結構是不會被馬上建立起來的，是要等應用第一次執行起來後才會。
* validate：每次載入hibernate時，驗證建立資料庫表結構，只會和資料庫中的表進行比較，不會建立新表，但是會插入新值。
* none：什麼都不做。

目前常用設定是使用 「none」 ，原因是資料表結構多是直接透過資料庫IDE進行操作，不直接由程式進行控制，開發流程上會先將資料表建置完成後才進行程式開發、測試、部署，而非開發後才進行資料表建置，同時也避免資料表資料被誤刪。
