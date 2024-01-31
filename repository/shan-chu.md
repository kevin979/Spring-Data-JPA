# 刪除

刪除資料的Method有以下幾種 「delete」、「deleteAll」、「deleteById」、「deleteAllById」、「deleteAllByIdInBatch」、「deleteAllInBatch」 ，同樣的，也可以自行撰寫SQL進行資料刪除，而刪除功能就沒有 「deleteAndFlush」 這類型的功能，所以刪除資料後若需要即時commit，則可以在delete後呼叫repository.flush()進行資料commit。

* delete、deleteById -> 此兩方法在底層使用的SQL都是where pk欄位 = id，只是delete需放入完整的entity，而deleteById 僅需放入ID即可。
* deleteAll、deleteAllById -> 此兩方法的引數都是List，其效果同上，只是在底層使用迴圈呼叫上述兩方法。
* deleteAllInBatch、deleteAllByIdInBatch -> 此兩方法較為特別，主要是在進行資料大量刪除時，倘若一次進行commit可能會造成資料庫大量負擔，所以有時候使用批次刪除，而此兩方法就是批次處理。
