---
title: 資料分割運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的資料分割運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2b082e516a1118638bc8e61b545471326dd400e5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346230"
---
# <a name="partition-operator"></a>partition 運算子

分割運算子會根據指定的資料行值，將其輸入資料表分割成多個子資料工作表、在每個子資料工作表上執行子查詢，並產生單一輸出資料表，這是所有子查詢結果的聯集。 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

## <a name="syntax"></a>語法

*T* `|` `partition` [*PartitionParameters*] 資料 `by` *行* `(` *CoNtextualSubquery*`)`

*T* `|` `partition` [*PartitionParameters*] 資料 `by` *行* `{` *子查詢*`}`

## <a name="arguments"></a>引數

* *T*：要由運算子處理其資料的表格式來源。

* *Column*： *T*中的資料行名稱，其值會決定輸入資料表的分割方式。 請參閱下面的**附注**。

* *CoNtextualSubquery*：表格式運算式，其來源是運算子的來源 `partition` ，範圍限定為單一*索引鍵值*。

* *子查詢*：不含來源的表格式運算式。 *金鑰*值可透過 `toscalar()` 呼叫取得。

* *PartitionParameters*：零或多個（以空格分隔）參數，格式為： *Name* `=` *值*，可控制運算子的行為。 支援下列參數：

  |名稱               |值         |描述|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |如果設定為， `true` 將會具體化運算子的來源 `partition` （預設值： `false` ）|
  |`hint.concurrency`|*Number*|提示系統 `partition` 應該以平行方式執行運算子的多個並行子查詢。 *預設值*：叢集的單一節點上的 CPU 核心數量（2到16個）。|
  |`hint.spread`|*Number*|提示系統並行子查詢執行應使用的節點數目 `partition` 。 *預設值*：1。|

## <a name="returns"></a>傳回

運算子會傳回將子查詢套用到輸入資料的每個分割區之結果的聯集。

**備註**

* 資料分割運算子目前受限於磁碟分割數目。
  可能會建立最多64個相異的分割區。
  如果分割區資料行（資料*行*）有超過64個相異值，則運算子會產生錯誤。

* 子查詢會隱含地參考輸入資料分割（子查詢中沒有資料分割的 "name"）。 若要在子查詢中多次參考輸入資料分割，請使用[as 運算子](asoperator.md)，如以下範例所示 **：資料分割參考**。

**範例： top-nested 案例**

在某些情況下，使用 `partition` 運算子（而不是使用[ `top-nested` 運算子](topnestedoperator.md)）來撰寫查詢變得更有效率，而下一個範例會執行子查詢 `summarize` ，並 `top` 從開始 `W` ：（懷俄明州，華盛頓，WEST 佛吉尼亞，威斯康辛）

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|EventType|State|事件|傷害|
|---|---|---|---|
|Hail|懷俄明州|108|0|
|高風|懷俄明州|81|5|
|冬季風暴|懷俄明州|72|0|
|繁重的雪|位於|82|0|
|高風|位於|58|13|
|野火|位於|29|0|
|Thunderstorm Wind|西維吉尼亞|180|1|
|Hail|西維吉尼亞|103|0|
|冬季氣象|西維吉尼亞|88|0|
|Thunderstorm Wind|威斯康辛|416|1|
|冬季風暴|威斯康辛|310|0|
|Hail|威斯康辛|303|1|

**範例：查詢非重迭資料分割**

有時候，在地圖/縮減樣式中，對非重迭的資料分割執行複雜的子查詢是很有用的（效能取向）。 下列範例顯示如何建立10個數據分割的手動散發匯總。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    summarize Count=count() by Source 
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|來源|計數|
|---|---|
|Trained Spotter|12770|
|執法機關|8570|
|公開|6157|
|Emergency Manager|4900|
|合作基金觀察者|3039|

**範例：查詢時間分割**

下列範例示範如何將查詢分割成 N = 10 個數據分割，其中每個資料分割會計算自己的計數，並在稍後將所有匯總到 TotalCount。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let N = 10;                 // Number of query-partitions
range p from 0 to N-1 step 1  // 
| partition by p            // Run the sub-query partitioned 
{
    StormEvents 
    | where hash(EventId, N) == toscalar(p) // Use toscalar() to fetch partition key value
    | summarize Count = count()
}
| summarize TotalCount=sum(Count) 
```

|TotalCount|
|---|
|59066|


**範例：資料分割-參考**

下列範例顯示如何使用[as 運算子](asoperator.md)，為每個資料分割提供「名稱」，然後在子查詢中重複使用該名稱：

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**範例：由函式呼叫隱藏的複雜子查詢**

同樣的技巧也可以運用更複雜的子查詢來套用。 為了簡化語法，其中一個可以將子查詢包裝在函式呼叫中：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let partition_function = (T:(Source:string)) 
{
    T
    | summarize Count=count() by Source
};
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    invoke partition_function()
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|來源|計數|
|---|---|
|Trained Spotter|12770|
|執法機關|8570|
|公開|6157|
|Emergency Manager|4900|
|合作基金觀察者|3039|