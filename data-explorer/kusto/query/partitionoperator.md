---
title: 資料分割運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的資料分割運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b6f2628bf2391ccea53e7fe70c76c341dda7f4e9
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324562"
---
# <a name="partition-operator"></a>partition 運算子

資料分割運算子會根據指定資料行的值，將其輸入資料表分割成多個子資料工作表、對每個子資料工作表執行子查詢，並產生單一輸出資料表，也就是所有子查詢結果的聯集。 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

## <a name="syntax"></a>Syntax

*T* `|` `partition` [*PartitionParameters*] 資料 `by` *行* `(` *CoNtextualSubquery*`)`

*T* `|` `partition` [*PartitionParameters*] 資料 `by` *行* `{` *子查詢*`}`

## <a name="arguments"></a>引數

* *T*：要由運算子處理其資料的表格式來源。

* 資料 *行*： *T* 中的資料行名稱，其值會決定如何分割輸入資料表。 請參閱下面的 **附注** 。

* *CoNtextualSubquery*：表格式運算式，其中來源是運算子的來源 `partition` ，範圍為單一 *索引鍵值* 。

* *子查詢*：不含來源的表格式運算式。 您可以透過呼叫取得 *金鑰* 值 `toscalar()` 。

* *PartitionParameters*：零或多個 (空間分隔) 參數的格式為： *名稱* `=` *值* ，可控制運算子的行為。 支援下列參數：

  |Name               |值         |說明|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |如果設定為，則 `true` 會具體化運算子的來源 `partition` (預設： `false`) |
  |`hint.concurrency`|*Number*|提示系統會以平行方式執行多少個數據分割。 *預設值*：16。|
  |`hint.spread`|*Number*|提示系統如何在叢集節點之間散發磁碟分割 (例如：如果有 N 個分割區，而散佈提示設定為 P，則根據並行提示) ，平均會以平行/順序平均處理 N 個分割區的不同叢集節點。 *預設值*：1。|

## <a name="returns"></a>傳回

運算子會傳回將子查詢套用至輸入資料之每個資料分割之結果的聯集。

**注意事項**

* 資料分割運算子目前受限於資料分割數目。
  最多可以建立64個不同的資料分割。
  如果分割區資料行 (資料 *行*) 有超過64個相異值，運算子將會產生錯誤。

* 子查詢會隱含地參考輸入資料分割， (子查詢) 中的資料分割沒有 "name"。 若要在子查詢中多次參考輸入資料分割，請使用 [as 運算子](asoperator.md)，如下列範例所示 **：資料分割參考** 。

**範例：最上層嵌套案例**

在某些情況下，使用運算子來撰寫查詢的效能更高且更容易， `partition` 而不是使用[ `top-nested` 運算子](topnestedoperator.md)，下一個範例會執行子查詢計算 `summarize` 和 `top` 每個狀態，開頭為 `W` ： (懷俄明州、華盛頓州、西佛吉尼亞州威斯康辛) 

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
|EventType|State|事件|損傷|
|---|---|---|---|
|Hail|懷俄明州|108|0|
|高風|懷俄明州|81|5|
|冬季風暴|懷俄明州|72|0|
|大雪|華盛頓|82|0|
|高風|華盛頓|58|13|
|野火|華盛頓|29|0|
|Thunderstorm Wind|西維吉尼亞|180|1|
|Hail|西維吉尼亞|103|0|
|冬季氣象|西維吉尼亞|88|0|
|Thunderstorm Wind|威斯康辛州|416|1|
|冬季風暴|威斯康辛州|310|0|
|Hail|威斯康辛州|303|1|

**範例：查詢非重迭的資料分割**

有時候， (效能取向的) ，以便在地圖/縮小樣式中對非重迭的資料分割執行複雜的子查詢，這會很有用。 下列範例示範如何建立在10個數據分割上的手動分佈匯總。

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
|合作的觀察者|3039|

**範例：查詢時間資料分割**

下列範例示範如何將查詢分割成 N = 10 個數據分割，其中每個資料分割都會計算自己的計數，而所有後續摘要都摘要到 TotalCount 中。

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


**範例：資料分割參考**

下列範例示範如何使用 [as 運算子](asoperator.md) 將「名稱」提供給每個資料分割，然後在子查詢中重複使用該名稱：

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**範例：函式呼叫隱藏的複雜子查詢**

同樣的技巧也可以套用至更複雜的子查詢。 為了簡化語法，您可以在函式呼叫中包裝子查詢：

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
|合作的觀察者|3039|
