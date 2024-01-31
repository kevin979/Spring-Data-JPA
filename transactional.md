# Transactional

使用JPA時，如有資料異動(insert、update、delete)，都會需要在Method上宣告 @Transactional，用意是告知JPA此為交易行為，必要時會進行rollback，而Transactional有以下屬性提供設定：

* name -> 如果有自定義的TransactionManager，可配置此屬性用來指定TransactionManager
* propagation -> 傳播性，預設是REQUIRED (後續會說明)
* isolation -> 隔離程度，預設是DEFAULT (後續會說明)
* timeout -> 交易時間(單位是秒)，預設是 -1 (不限時)，如有設定此屬性須注意，一但超過交易時間，則交易視為失敗會自動rollback
* readOnly -> 唯讀，預設是false，如果設為true，則所有交易行為將會失效，僅能讀取數據
* rollbackFor -> 當程式遇到指定的Exception後會自動rollback，如果沒有配置此屬性，則交易時遇到RuntimeException才會進行rollback
* noRollbackFor -> 同上，但效果是相反的，當遇到指定Exception時不進行rollback

propagation(傳播性)，此設定值的用意是當兩個Transaction相碰時所需進行的處理，什麼情況會遇到兩個Transaction呢？舉例來說：新增一筆注單時會更新會員資訊(下注時間)、新增交易紀錄等等，傳統在寫這些交易行為時是利用Connection進行傳播，但在JPA中Connection已經被Transactional取代掉了，那要怎樣確保交易過程中Connection是同一個或者是異步處理？而propagation這屬性就是用來處理這類型的案例，因此propagation有非常多屬性可以設定：

* Propagation.MANDATORY -> 支持當前 Transaction，如果當前 Transaction 不存在，則拋出Exception。
* Propagation.NESTED -> 如果當前 Transaction 存在，則在當前Transaction 中執行。
* Propagation.NEVER -> 不支持當前 Transaction，如果當前 Transaction 存在，則拋出Exception。
* Propagation.NOT\_SUPPORTED -> 不支持當前 Transaction；而是始終以非 Transaction 方式執行。
* Propagation.REQUIRED -> (預設值) 支持當前 Transaction，如果不存在，則創建一個新的。
* Propagation.REQUIRES\_NEW -> 創建一個新 Transaction，如果 Transaction 存在則暫停當前 Transaction。
* Propagation.SUPPORTS -> 支持當前 Transaction，如果不存在，則以非 Transaction 方式執行。

isolation(隔離)，此設定值是針對多交易行為時，如果有共用到相同的資料，那資料間可能會造成Dirty Read之類的狀況，畢竟每一個交易行為都是獨立互不干涉，所以針對交易行為的資料獨立性會需要進行設定，在設定之前需先說明資料會產生哪些狀況：

* Dirty Read -> 當有多筆交易在進行時，假設有一筆資料在異動前為1000，TransactionA第一次讀取時為1000，接著TransactionB進行了資料異動，將資料改為700，此時交易尚未完成，所以資料還沒Commit，但TransactionA再次讀取資料時卻發現資料已異動為700了，此現象就是Dirty Read，TransactionA讀取的資料不是實際資料庫的資料，而是session中尚未提交的資料。
* Non-repeatable reads -> 在同一個Transaction中多次執行同一個SQL但結果卻都不盡相同，此現象就是Non-repeatable reads，而Dirty Read就是一種Non-repeatable reads。
* Phantom reads -> 當在同一個Transaction連續兩次讀取時，讀取出來的筆數跟上次不同，這個情況稱為 Phantom reads。

isolation的設定就是為了解決上述所闡述的現象，避免資料產生錯誤，而設定值有以下幾種：

* Isolation.DEFAULT -> (預設值) 使用資料庫預設的隔離層級。
* Isolation.READ\_UNCOMMITTED -> 允許交易讀取其它並行的交易還沒Commit的資料欄位。 (不建議使用)
* Isolation.READ\_COMMITTED -> 允許交易讀取其它並行的交易已經Commit的資料。
* Isolation.REPEATABLE\_READ -> 要求多次讀取的資料必須相同，除非交易本身更新資料。
* Isolation.SERIALIZABLE -> 完整的隔離層級，會鎖定對應的資料表格，有效能問題。

一般來說不會特別設置該設定，因為資料庫本身就有isolation的設定，所以多數會跟隨著資料庫的設定進行操作，除非有特殊交易行為才會進行設定，譬如：鎖表

附註：MySQL預設的隔離層級是REPEATABLE\_READ
