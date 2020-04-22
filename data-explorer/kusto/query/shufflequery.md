---
title: 隨機播放查詢 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的隨機播放查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c687d495a41a5f73ac8dbca15d93729f2132a556
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81662983"
---
# <a name="shuffle-query"></a>隨機播放查詢

隨機播放查詢是一組支援隨機操作策略的運算符的語義保留轉換,根據實際數據可以產生更好的性能。

支援Kusto洗牌的營運商是[加入](joinoperator.md),[總結](summarizeoperator.md)與[製作系列](make-seriesoperator.md)。

隨機查詢策略可以由查詢參數`hint.strategy = shuffle``hint.shufflekey = <key>`或設置。

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

此策略將在所有群集節點上共用負載,其中每個節點將處理一個數據分區。
當鍵(`join`鍵、鍵或`summarize``make-series`鍵)具有高基數導致常規查詢策略達到查詢限制時,使用隨機查詢策略非常有用。

**提示.策略_隨機和提示之間的差異.**

`hint.strategy=shuffle`意味著洗牌運算符將被所有鍵洗牌。
例如,在此查詢中:

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

隨機播放數據的哈希函數將使用兩個鍵 ActivityId 和 ProcessId。

上面查詢等效於 :

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

當您有興趣按隨機操作運算符的所有鍵洗牌數據時,可以使用此提示,因為複合鍵太獨特,但每個鍵都不夠唯一。
當隨機操作者具有其他可洗牌運算符(如`summarize`或`join`)時,查詢變得更加複雜,然後提示。

例如 :

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

在這種情況下,如果我們應用`hint.strategy=shuffle`(而不是在查詢計劃期間忽略策略) 並按複合`ActivityId`鍵 *`numeric_column`隨機排列數據 , * 的結果將不正確。
在`summarize``join`凹槽左側的,由鍵的`join`子集(即`ActivityId`) 這意味著,`summarize`當資料由複合鍵`ActivityId``ActivityId` `numeric_column`[ 、 ] 進行分區時,將按鍵分組。
按鍵 [`ActivityId`、 `numeric_column`] 進行洗牌並不意味著它是密鑰活動 Id 的有效洗牌,結果可能不正確。

本示例簡化此簡單,假定用於複合鍵的哈希函數是`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|數位柱|hash_by_key|
|---|---|---|
|活動1|2|56|
|活動1|1|65|



當您看到`ActivityId`兩個記錄的複合鍵映射到不同的分區 56 和 65,但這兩個記錄具有相同`summarize`的值,`join`這意味著的左側`ActivityId`, 其中預計列的類似值在同一分區將定義會產生錯誤的結果。

在這種情況下,`hint.shufflekey`通過指定聯接上的洗牌鍵來解決此`hint.shufflekey = ActivityId`問題 ,這是所有可洗牌運算符的通用密鑰。
在這種情況下,洗牌是安全的,同時`join`由同一`summarize`鍵 隨機排列,因此所有類似的值都將在同一分區中,結果是正確的:

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

|ActivityId|數位柱|hash_by_key|
|---|---|---|
|活動1|2|56|
|活動1|1|65|

在隨機播放查詢中,預設分區編號是群集節點編號。 可以使用控制分區數的語法`hint.num_partitions = total_partitions`來覆蓋此數位。

當群集具有少量群集節點,其中預設分區數也較小,並且查詢仍然失敗或執行時間過長時,此提示非常有用。

請注意,設置多個分區可能會降低性能並消耗更多的群集資源,因此建議謹慎選擇分區編號(從 hint.at.

**範例**

下面的示例顯示了隨機播放`summarize`如何顯著提高性能。

源表具有 150M 記錄,按鍵分組的基數為 10M,分佈在 10 個群集節點上。

運行常規`summarize`策略后,查詢在 1:08 后結束,記憶體使用高峰為 +3GB:

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

使用隨機播放`summarize`策略時,查詢在 +7 秒後結束,記憶體使用峰值為 0.43GB:

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

下面的範例顯示了具有 2 個群集節點的群集的改進,表具有 60M 記錄,按鍵分組的基數為 2M。

在沒有運行查詢的情況下`hint.num_partitions`,將僅使用 2 個分區(作為群集節點編號),以下查詢需要 ±1:10 分鐘:

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

將分割區數設定為 10,查詢將在 23 秒後結束: 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

下面的示例顯示了隨機播放`join`如何顯著提高性能。

這些示例在具有 10 個節點的群集上進行了採樣,其中數據分佈在所有這些節點上。

左表有 15M`join`記錄, 其中金鑰的基數為 +14M,右側`join`為 150M 記錄,`join`密鑰的基數為 10M。
執行的常規政策`join`, 查詢在 +28 秒後結束, 記憶體使用峰值為 1.43GB :

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

使用隨機播放`join`策略時,查詢在 +4 秒後結束,記憶體使用峰值為 0.3GB :

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

在較大的數據集上嘗試相同的查詢,其中左側`join`為 150M,密鑰的基數為 148M,右側`join`為 1.5B,密鑰的基數為 ±100M。

使用預設`join`策略的查詢在 4 分鐘后達到 kusto 限制和超時。
使用隨機播放`join`策略時,查詢在 +34 秒后結束,記憶體使用峰值為 1.23GB。


下面的範例顯示了具有 2 個群集節點的群集的改進,表具有 60M 記錄,`join`密鑰的基數為 2M。
在沒有運行查詢的情況下`hint.num_partitions`,將僅使用 2 個分區(作為群集節點編號),以下查詢需要 ±1:10 分鐘:

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

將分割區數設定為 10,查詢將在 23 秒後結束: 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```