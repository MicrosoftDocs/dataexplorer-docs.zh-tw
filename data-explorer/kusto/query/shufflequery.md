---
title: 隨機查詢-Azure 資料總管
description: 本文說明 Azure 資料總管中的隨機查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7f9388b85b673237ca676f828fc093b01b2e69d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241933"
---
# <a name="shuffle-query"></a>隨機查詢

隨機查詢是一組支援隨機策略之運算子的語義保留轉換。 視實際的資料而定，此查詢可能會產生相當好的效能。

在 Kusto 中支援跳過的運算子包括 [聯結](joinoperator.md)、 [摘要](summarizeoperator.md)和 [構成系列](make-seriesoperator.md)。

使用查詢參數或，設定隨機查詢 `hint.strategy = shuffle` 策略 `hint.shufflekey = <key>` 。

## <a name="syntax"></a>語法

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

此策略將會在所有叢集節點上共用負載，其中每個節點都會處理資料的一個資料分割。
當索引鍵 (索引鍵 `join` 、索引 `summarize` 鍵或索引鍵 `make-series`) 具有高基數且一般查詢策略達到查詢限制時，使用隨機查詢策略會很有用。

**提示之間的差異。策略 = 隨機和提示。 shufflekey = 索引鍵**

`hint.strategy=shuffle` 表示隨機運算子將會由所有索引鍵隨機標示。
例如，在此查詢中：

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

洗牌資料的雜湊函式會使用索引鍵 ActivityId 和 ProcessId。

上述查詢相當於：

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

如果複合索引鍵太是唯一的，但每個索引鍵的用途都不是唯一的，請使用此索引鍵， `hint` 以隨機運算子的所有索引鍵來亂數據。
當隨機運算子具有其他可隨機操作的運算子時（例如 `summarize` 或 `join` ），查詢會變得更複雜，然後提示。不會套用策略 = 隨機的。

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

如果您套用 `hint.strategy=shuffle` (，而不是在查詢計劃期間忽略策略) 並依複合索引鍵 [，] 隨機使用資料 `ActivityId` `numeric_column` ，則結果將會不正確。
`summarize`運算子位於運算子的左邊 `join` 。 這個運算子會依索引鍵的子集分組 `join` ，在我們的案例中為 `ActivityId` 。 因此， `summarize` 會依索引鍵分組 `ActivityId` ，而資料會依複合索引鍵 [ `ActivityId` ，] 進行分割 `numeric_column` 。
依複合索引鍵跳過 [ `ActivityId` ， `numeric_column` ] 不一定表示索引鍵的跳過 `ActivityId` 有效，且結果可能不正確。

這個範例假設用於複合索引鍵的雜湊函式是 `binary_xor(hash(key1, 100) , hash(key2, 100))` ：

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

這兩筆記錄的複合索引鍵對應到不同的資料分割 (56 和 65) ，但是這兩筆記錄的值相同 `ActivityId` 。 `summarize`左邊的運算子在相同的 `join` 資料分割中應有類似的資料行值 `ActivityId` 。 此查詢將會產生不正確的結果。

您可以使用 `hint.shufflekey` 來指定聯結上的隨機索引鍵，以解決這個問題 `hint.shufflekey = ActivityId` 。 此索引鍵對於所有可隨機操作的運算子都很常見。
在此情況下，跳過是安全的，因為兩者都是 `join` `summarize` 使用相同的金鑰來隨機播放。 因此，所有類似的值都將位於相同的資料分割中，且結果正確：

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

在隨機查詢中，預設分割區數目是叢集節點編號。 您可以使用語法來覆寫這個數位 `hint.num_partitions = total_partitions` ，以控制資料分割數目。

當叢集有少數叢集節點，其中的預設分割區數目也很小，而且查詢仍然失敗或花費很長的執行時間時，這個提示就很有用。

> [!Note]
> 有許多磁碟分割可能會耗用更多的叢集資源，並降低效能。 相反地，請從提示開始，仔細選擇資料分割編號。策略 = 隨機分割，並逐漸開始增加資料分割。

## <a name="examples"></a>範例

下列範例將示範隨機可如何 `summarize` 大幅改善效能。

來源資料表具有150M 記錄，而 group by 索引鍵的基數是1千萬個，其分佈超過10個叢集節點。

`summarize`執行一般策略時，查詢會在1:08 之後結束，且記憶體使用量尖峰為 ~ 3 GB：

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|計數|
|---|
|1086|

使用隨機 `summarize` 策略時，查詢會在 ~ 7 秒後結束，且記憶體使用量尖峰為 0.43 GB：

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|計數|
|---|
|1086|

下列範例顯示具有兩個叢集節點的叢集上的改進、資料表具有60M 記錄，以及 group by 索引鍵的基數為2M。

執行查詢時，不 `hint.num_partitions` 會使用兩個數據分割 (作為叢集節點編號) 而且下列查詢將需要 ~ 1:10 分鐘：

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

將資料分割數目設定為10，查詢將會在23秒後結束： 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

下列範例將示範隨機可如何 `join` 大幅改善效能。

範例是在含有10個節點的叢集上進行取樣，其中資料會散佈在所有這些節點上。

左邊的資料表具有1,500 萬次記錄，其中索引鍵的基數 `join` 是 ~ 14M。 右側 `join` 是具有150M 記錄，而索引鍵的基數 `join` 為1千萬個。
執行的一般策略 `join` ，查詢會在 ~ 28 秒後結束，且記憶體使用量尖峰為 1.43 GB：

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

使用隨機 `join` 策略時，查詢會在 ~ 4 秒後結束，且記憶體使用量尖峰為 0.3 GB：

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

在較大的資料集上嘗試相同的查詢，其左邊 `join` 為150M，而索引鍵的基數為148M。 右側 `join` 是 1.5 b，索引鍵的基數是 ~ 100M。

具有預設策略的查詢會 `join` 在4分鐘後到達 Kusto 限制和超時時間。
使用隨機 `join` 策略時，查詢會在 ~ 34 秒後結束，且記憶體使用量尖峰為 1.23 GB。


下列範例顯示具有兩個叢集節點的叢集上的改進、資料表具有60M 記錄，以及索引鍵的基數 `join` 為2M。
執行查詢時，不 `hint.num_partitions` 會使用兩個數據分割 (作為叢集節點編號) 而且下列查詢將需要 ~ 1:10 分鐘：

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

將資料分割數目設定為10，查詢將會在23秒後結束： 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```
