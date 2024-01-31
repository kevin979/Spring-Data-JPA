# 為什麼要使用JPA

以往訪問資料庫時，最基本會進行DAO分層架構，但是過往需要撰寫大量的SQL，造成維護上的困難以及程式碼大量重複。

如果團隊開發對於DAO建構無相當規範，也會造成程式碼難以閱讀、難以維護，而ORM就是為此而誕生。

目前常見ORM有Hibernate、MyBatis、Spring Data JPA...等等，而目前JAVA主流框架為Spring boot，因此建議使用官方推薦的Spring Data JPA。

