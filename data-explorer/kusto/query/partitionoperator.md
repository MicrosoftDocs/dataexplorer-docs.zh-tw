---
title: 分割區運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的分區運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0a9ef31f68a989fffe42d9b54800e9305e51f4ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511368"
---
# <a name="partition-operator"></a>partition 運算子

分區運算符根據指定列的值將其輸入表分區到多個子表中,在每個子表上執行子查詢,並生成單個輸出表,該輸出表是所有子查詢結果的合併。 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

**語法**

*T* `partition` `by` *Column*`(`*ContextualSubquery* T =*分割區參數*= 欄 中查詢`|``)`

*T* `partition` `by` *Column*`{`*Subquery* T =*分割區參數*= 欄子查詢`|``}`

**引數**

* *T*: 其資料將由運算子處理的表格來源。

* *列* *:T*中一列的名稱,其值確定如何劃分輸入表。 請參考下一個下註**解**。

* *上下文子查詢*:一個表格表達式,該表單表達式,該`partition`表 單表達式是運算符的源,為單個*鍵*值範圍。

* *子查詢*:沒有源的表格運算式。 *可以通過*`toscalar()`呼叫獲得密鑰值。

* *分割區參數*:零個或多個(空間分隔)參數,其形式為:控制運算子行為*的名稱*`=`*值*。 支援下列參數：

  |名稱               |值         |描述|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |設定為`true`將運算子的`partition`來源(預設值: `false`)|
  |`hint.concurrency`|*數量*|提示系統應並行執行`partition`運算符的併發子查詢數。 *默認值*:群集單個節點上的 CPU 內核量(2 到 16)。|
  |`hint.spread`|*數量*|提示系統併發`partition`子查詢執行應使用多少個節點。 *默認值*:1。|

**傳回**

運算符返回將子查詢應用於輸入數據的每個分區的結果的聯合。

**注意事項**

* 分區運算符當前受分區數的限制。
  最多可以創建 64 個不同的分區。
  如果分割區列 (*列*) 具有超過 64 個不同的值,則運算元將生成錯誤。

* 子查詢隱式引用輸入分區(子查詢中沒有分區的"名稱")。 您要在子查詢中多次引用輸入分區,請使用[為運算子](asoperator.md),如**下面的範例:分區參考**。

**範例:頂端嵌so大小寫**

在某些情況下`partition`- 使用運算符而不是使用[`top-nested`運算符](topnestedoperator.md)編寫查詢更性能、更容易 下一個範例運行`summarize`子查詢`top`計算 ,並且`W`針對每個國家從 :(威明、華盛頓、西弗吉尼亞、威斯康星)開始

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
|大風|懷俄明州|81|5|
|冬季風暴|懷俄明州|72|0|
|大雪|華盛頓|82|0|
|大風|華盛頓|58|13|
|野火|華盛頓|29|0|
|Thunderstorm Wind|西維吉尼亞|180|1|
|Hail|西維吉尼亞|103|0|
|冬季天氣|西維吉尼亞|88|0|
|Thunderstorm Wind|威斯康辛州|416|1|
|冬季風暴|威斯康辛州|310|0|
|Hail|威斯康辛州|303|1|

**範例:查詢非重疊資料分割區**

有時,在地圖/減少樣式中對非重疊數據分區運行複雜的子查詢很有用(按明智方式)。 下面的示例演示如何創建超過 10 個分區的聚合的手動分佈。

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

|來源|Count|
|---|---|
|Trained Spotter|12770|
|執法機關|8570|
|公開|6157|
|Emergency Manager|4900|
|COOP 觀察員|3039|

**範例:查詢時間分區**

下面的示例演示如何將查詢分區劃分為 N=10 分區,其中每個分區計算其自己的 Count,然後將所有分區匯總到 TotalCount 中。

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


**範例:分割區參照**

下面的範例展示如何使用[as 運算子](asoperator.md)為每個資料分區指定一個「name」,然後在子查詢中重用該名稱:

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**範例:由函數呼叫隱藏的複雜子查詢**

同樣的技術可以應用於更複雜的子查詢。 為了簡化語法,可以在函數調用中包裝子查詢:

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

|來源|Count|
|---|---|
|Trained Spotter|12770|
|執法機關|8570|
|公開|6157|
|Emergency Manager|4900|
|COOP 觀察員|3039|