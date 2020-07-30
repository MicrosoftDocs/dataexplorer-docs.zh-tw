---
title: 隨機查詢-Azure 資料總管
description: 本文說明 Azure 資料總管中的隨機查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e011ffa61b70c79d51941518de0624030d847c4e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351092"
---
# <a name="shuffle-query"></a>隨機查詢

隨機查詢是一組支援隨機播放策略之運算子的語義保留轉換。 視實際資料而定，此查詢可能會產生顯著的效能提升。

在 Kusto 中支援隨機的運算子包括 [[聯結](joinoperator.md)]、[[摘要](summarizeoperator.md)] 和 [[構成系列](make-seriesoperator.md)]。

使用查詢參數或來設定隨機查詢 `hint.strategy = shuffle` 策略 `hint.shufflekey = <key>` 。

在資料表上定義[資料分割原則](../management/partitioningpolicy.md)。 

設定 `shufflekey` 為數據表的雜湊分割區索引鍵，以達到更佳的效能，因為在叢集節點之間移動所需的資料量會降低。

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

此策略會共用所有叢集節點上的負載，其中每個節點都會處理資料的一個分割區。
當索引鍵（索引鍵、索引 `join` `summarize` 鍵或索引 `make-series` 鍵）具有高基數和一般查詢策略點擊查詢限制時，使用隨機查詢策略會很有用。

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

如果複合索引鍵不是唯一的，但每個索引鍵都不是唯一的，請使用此索引鍵， `hint` 依隨機運算子的所有索引鍵隨機播放資料。
當隨機運算子具有其他可隨機操作的運算子（例如 `summarize` 或 `join` ）時，查詢會變得更複雜，然後才會提示。將不會套用 [策略 = 隨機播放]。

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

如果您套用 `hint.strategy=shuffle` （而不是在查詢計劃期間忽略策略），並依複合索引鍵 [，] 隨機播放資料 `ActivityId` `numeric_column` ，則結果將會不正確。
`summarize`運算子位於運算子的左邊 `join` 。 這個運算子會依索引鍵的子集分組 `join` ，在我們的案例中為 `ActivityId` 。 因此，會使用索引鍵來 `summarize` 分組 `ActivityId` ，而資料則是以複合索引鍵 [ `ActivityId` ，] 進行分割 `numeric_column` 。
隨機 by 複合索引鍵 [ `ActivityId` ， `numeric_column` ] 不一定表示索引鍵的隨機 `ActivityId` 是有效的，而且結果可能不正確。

這個範例假設用於複合索引鍵的雜湊函數為 `binary_xor(hash(key1, 100) , hash(key2, 100))` ：

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

這兩個記錄的複合索引鍵都對應到不同的資料分割（56和65），但這兩個記錄的值相同 `ActivityId` 。 `summarize`左側的運算子 `join` 需要資料行的類似值 `ActivityId` ，才會在同一個分割區中。 此查詢會產生不正確的結果。

若要解決這個問題，您可以使用 `hint.shufflekey` 來指定聯結上的隨機播放索引鍵 `hint.shufflekey = ActivityId` 。 這個金鑰組于所有可隨機操作的運算子而言都是常見的。
在此情況下，隨機是安全的，因為和都是以 `join` `summarize` 相同的金鑰隨機播放。 因此，所有類似的值都會位於相同的分割區中，且結果會正確：

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

在隨機查詢中，預設的分割區編號是叢集節點編號。 您可以使用語法來覆寫這個數位 `hint.num_partitions = total_partitions` ，這會控制分割區的數目。

當叢集有少數的叢集節點，而且預設分割區數目也很小，而且查詢仍然失敗，或花費較長的執行時間時，此提示就很有用。

> [!Note]
> 有許多分割區可能會耗用更多的叢集資源並降低效能。 相反地，請從提示開始，仔細選擇資料分割編號。策略 = 隨機播放，並逐漸開始增加磁碟分割。

## <a name="examples"></a>範例

下列範例顯示隨機執行如何 `summarize` 大幅改善效能。

來源資料表具有150M 記錄，而 group by 索引鍵的基數是1千萬個，它會散佈在10個以上的叢集節點上。

`summarize`執行一般策略時，查詢會在1:08 之後結束，而記憶體使用量尖峰為 ~ 3 GB：

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|計數|
|---|
|1086|

使用隨機播放 `summarize` 策略時，查詢會在 ~ 7 秒後結束，而記憶體使用量尖峰為 0.43 GB：

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|計數|
|---|
|1086|

下列範例顯示具有兩個叢集節點的叢集上的改善、資料表具有60M 記錄，以及 group by 索引鍵的基數是2M。

執行查詢時，不 `hint.num_partitions` 會使用兩個分割區（作為叢集節點編號），而下列查詢將會花費 ~ 1:10 分鐘：

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

下列範例顯示隨機執行如何 `join` 大幅改善效能。

範例是在具有10個節點的叢集上取樣，其中的資料會分散在所有這些節點上。

左側資料表具有1,500 萬次記錄，其中索引鍵的基數 `join` 為 ~ 14M。 的右側 `join` 是使用150M 記錄，而索引鍵的基數 `join` 是1千萬個。
執行的一般策略 `join` 時，查詢會在 ~ 28 秒後結束，而記憶體使用量尖峰為 1.43 GB：

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

使用隨機播放 `join` 策略時，查詢會在 ~ 4 秒後結束，而記憶體使用量尖峰為 0.3 GB：

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

在較大的資料集上嘗試相同的查詢，其中的左邊 `join` 是150M，而索引鍵的基數是148M。 的右側 `join` 是 1.5 b，而索引鍵的基數是 ~ 100M。

具有預設策略的查詢會 `join` 在4分鐘後到達 Kusto 限制和超時時間。
使用隨機播放 `join` 策略時，查詢會在 ~ 34 秒後結束，而記憶體使用量尖峰為 1.23 GB。


下列範例顯示具有兩個叢集節點的叢集上的改善、資料表具有60M 記錄，而且索引鍵的基數 `join` 是2M。
執行查詢時，不 `hint.num_partitions` 會使用兩個分割區（作為叢集節點編號），而下列查詢將會花費 ~ 1:10 分鐘：

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
