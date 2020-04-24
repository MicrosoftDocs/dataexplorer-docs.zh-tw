---
title: 隨機查詢-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的隨機查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 600e561937b779ff9dd10d5d82f5522d204466a0
ms.sourcegitcommit: 2e63c7c668c8a6200f99f18e39c3677fcba01453
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117687"
---
# <a name="shuffle-query"></a>隨機查詢

隨機查詢是一組運算子的語義保留轉換，支援隨機播放策略，視實際資料而定，可大幅提升效能。

在 Kusto 中支援隨機的運算子包括[join](joinoperator.md)、[總結](summarizeoperator.md)和[make 系列](make-seriesoperator.md)。

隨機查詢策略可以透過查詢參數`hint.strategy = shuffle`或`hint.shufflekey = <key>`來設定。

您也可以在資料表上查看定義[資料分割原則](../management/partitioningpolicy.md)。 也`shufflekey`就是資料表的雜湊分割區索引鍵的查詢，因為在叢集節點之間移動所需的資料量已大幅減少，所以效能應該更好。

**語法**

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

```kusto
T
| summarize hint.strategy = shuffle count(), avg(price) by supplier
```

```kusto
T
| make-series hint.shufflekey = Fruit PriceAvg=avg(Price) default=0  on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

此策略會共用所有叢集節點上的負載，其中每個節點都會處理資料的一個分割區。
當索引鍵（`join`索引鍵、 `summarize`索引鍵或`make-series`索引鍵）的基數高，而使一般查詢策略達到查詢限制時，使用隨機查詢策略會很有用。

**提示之間的差異。策略 = 隨機和提示。 shufflekey = key**

`hint.strategy=shuffle`表示所有按鍵都會隨機加上隨機操作的運算子。
例如，在此查詢中：

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

洗牌資料的雜湊函數會同時使用索引鍵 ActivityId 和 ProcessId。

上述查詢相當於：

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

當您想要依據隨機運算子的所有索引鍵來亂數據時，可以使用這個提示，因為複合索引鍵太不是唯一的，但每個索引鍵的唯一性都不夠。
當隨機運算子具有或`summarize` `join`之類的其他 shufflable 運算子時，查詢會變得更複雜，然後提示。將不會套用策略 = 隨機播放。

例如：

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.strategy = shuffle (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

在此情況下，如果我們確實套用`hint.strategy=shuffle` （而不是在查詢計劃期間忽略策略），並依複合索引鍵 [`ActivityId`， `numeric_column`] 隨機播放資料，則結果將會不正確。
， `summarize`其位於`join` groubs 左邊的，其為的索引`join`鍵子集。 `ActivityId` 這表示當資料`summarize`依複合索引鍵 [ `ActivityId` `ActivityId`， `numeric_column`] 進行分割時，將會以索引鍵分組。
隨機 by 複合索引鍵 [`ActivityId`， `numeric_column`] 並不表示它是索引鍵 ActivityId 的有效隨機，而且結果可能不正確。

這個範例會簡化這種情況，假設用於複合索引鍵的雜湊函數是`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|activity1|2|56|
|activity1|1|65|



當您看到這兩個記錄的複合索引鍵對應到不同的分割區56和65，但這兩個記錄`ActivityId`具有相同的值`summarize` ，這表示左邊`join`的會預期資料行`ActivityId`的類似值會在相同的分割區中 defintely 產生錯誤的結果。

在此情況下`hint.shufflekey` ，會藉由在聯結上指定隨機按鍵（這`hint.shufflekey = ActivityId`是所有 shuffelable 運算子的共同索引鍵）來解決此問題。
在此情況下，隨機是安全的， `join`而且`summarize`是由相同的索引鍵來洗牌，因此所有類似的值都會 defintely 在相同的分割區中，結果會是正確的：

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.shufflekey = ActivityId (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|activity1|2|56|
|activity1|1|65|

在隨機查詢中，預設的分割區編號是叢集節點編號。 您可以使用會控制資料分割數目的`hint.num_partitions = total_partitions`語法來覆寫這個數位。

當叢集有少數的叢集節點，而且預設分割區數目也很小，而且查詢仍然失敗，或花費較長的執行時間時，此提示就很有用。

請注意，設定許多分割區可能會降低效能並耗用較多的叢集資源，因此建議您仔細選擇磁碟分割編號（從提示開始）。

**範例**

下列範例顯示隨機`summarize`執行如何大幅改善效能。

來源資料表具有150M 記錄，而 group by 索引鍵的基數是1千萬個，它會散佈超過10個叢集節點。

執行一般`summarize`策略時，查詢會在1:08 之後結束，而記憶體使用量尖峰為 ~ 3gb：

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

使用隨機播放`summarize`策略時，查詢會在 ~ 7 秒後結束，而記憶體使用量尖峰為 0.43 gb：

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

下列範例顯示具有2個叢集節點的叢集上的改善、資料表具有60M 記錄，而 group by 索引鍵的基數是2M。

執行查詢時， `hint.num_partitions`不會使用2個分割區（作為叢集節點編號），而下列查詢將會花費 ~ 1:10 分鐘：

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

將資料分割數目設定為10，查詢將于23秒後結束： 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

下列範例顯示隨機`join`執行如何大幅改善效能。

範例是在具有10個節點的叢集上取樣，其中的資料會分散在所有這些節點上。

左邊的資料表具有1,500 萬次記錄，其中索引`join`鍵的基數是 ~ 14M，而的右側`join`則是 with 150M 記錄，而索引`join`鍵的基數則是1千萬個。
執行的一般策略時`join`，查詢會在 ~ 28 秒後結束，而記憶體使用量尖峰則為 1.43 gb：

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

使用隨機播放`join`策略時，查詢會在 ~ 4 秒後結束，而記憶體使用量尖峰為 0.3 gb：

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

在150M 左側的`join`較大型資料集上嘗試相同的查詢，而且索引鍵的基數是148M，右邊`join`是 1.5 b，而索引鍵的基數是 ~ 100M。

具有預設`join`策略的查詢會在4分鐘後到達 kusto 限制和超時時間。
使用隨機播放`join`策略時，查詢會在 ~ 34 秒後結束，記憶體使用量尖峰則為 1.23 gb。


下列範例顯示具有2個叢集節點的叢集上的改善、資料表具有60M 記錄，而且索引`join`鍵的基數是2M。
執行查詢時， `hint.num_partitions`不會使用2個分割區（作為叢集節點編號），而下列查詢將會花費 ~ 1:10 分鐘：

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

將資料分割數目設定為10，查詢將于23秒後結束： 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```
