---
title: 具體化（）-Azure 資料總管
description: 本文說明 Azure 資料總管中的具體化（）函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/06/2020
ms.openlocfilehash: 0e08857e01ffa3da1bcb23d16d1df4908336f76f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346859"
---
# <a name="materialize"></a>materialize()

允許在查詢執行期間以其他子查詢可以參考部分結果的方式，來快取子查詢結果。
 
## <a name="syntax"></a>語法

`materialize(`*expression*`)`

## <a name="arguments"></a>引數

* *expression*：要在查詢執行期間評估和快取的表格式運算式。

> [!NOTE]
> 具體化的快取大小限制為**5 GB**。 這是每個叢集節點的限制，而且會與同時執行的所有查詢相互同步。 如果查詢使用 `materialize()` ，而且快取無法保存任何其他資料，則查詢將會中止並產生錯誤。

>[!TIP]
>
>* 推送可減少具體化資料集的所有可能運算子，並保留查詢的語法。 例如，使用篩選準則，或只投影必要的資料行。
>* 當其運算元具有可執行一次的相互子查詢時，請使用具體化搭配 join 或 union。 例如，聯結/聯集分支支線。 請參閱[使用 join 運算子的範例](#examples-of-query-performance-improvement)。
>* 當您提供快取的結果名稱時，具體化只能在 let 語句中使用。 請參閱[使用 let 語句的範例](#examples-of-using-materialize)）。

## <a name="examples-of-query-performance-improvement"></a>查詢效能改進的範例

下列範例顯示如何 `materialize()` 使用來改善查詢的效能。
運算式 `_detailed_data` 是使用函數所定義 `materialize()` ，因此只會計算一次。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _detailed_data = materialize(StormEvents | summarize Events=count() by State, EventType);
_detailed_data
| summarize TotalStateEvents=sum(Events) by State
| join (_detailed_data) on State
| extend EventPercentage = Events*100.0 / TotalStateEvents
| project State, EventType, EventPercentage, Events
| top 10 by EventPercentage
```

|State|EventType|EventPercentage|事件|
|---|---|---|---|
|夏威夷 WATERS|Waterspout|100|2|
|LAKE 安大略|航海 Thunderstorm 風|100|8|
|阿拉斯加|Waterspout|100|4|
|大西洋北部|航海 Thunderstorm 風|95.2127659574468|179|
|LAKE ERIE F.。。|航海 Thunderstorm 風|92.5925925925926|25|
|E 太平洋|Waterspout|90|9|
|LAKE 密歇根|航海 Thunderstorm 風|85.1648351648352|155|
|LAKE HURON|航海 Thunderstorm 風|79.3650793650794|50|
|墨西哥|航海 Thunderstorm 風|71.7504332755633|414|
|夏威夷|高度衝浪|70.0218818380744|320|


下列範例會產生一組亂數字，並計算： 
* 集合中有多少相異值（ `Dcount` ）
* 集合中的前三個值 
* 集合中所有這些值的總和 
 
這種作業可以使用[批次](batches.md)和具體化來完成：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let randomSet = 
    materialize(
        range x from 1 to 3000000 step 1
        | project value = rand(10000000));
randomSet | summarize Dcount=dcount(value);
randomSet | top 3 by value;
randomSet | summarize Sum=sum(value)
```

結果集1：  

|Dcount|
|---|
|2578351|

結果集2： 

|value|
|---|
|9999998|
|9999998|
|9999997|

結果集3： 

|加總|
|---|
|15002960543563|

## <a name="examples-of-using-materialize"></a>使用具體化（）的範例

> [!TIP]
> 如果大部分的查詢都從動態物件中的數百萬個數據列提取欄位，請在內嵌時具體化您的資料行。

若要使用 `let` 語句搭配您使用的值超過一次，請使用[具體化（）函數](./materializefunction.md)。 嘗試推送會減少具體化資料集的所有可能運算子，但仍保留查詢的語法。 例如，使用篩選準則，或只投影必要的資料行。

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
```

上的篩選 `Text` 是相互的，而且可以推送至具體化運算式。
查詢只需要、、 `Timestamp` 和資料行 `Text` `Resource1` `Resource2` 。 將這些資料行投影到具體化運算式內。
    
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
```
    
如果篩選準則不相同，如下列查詢所示：  

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
 ```

當結合的篩選準則大幅減少具體化的結果時，請依照下列查詢中的邏輯運算式，將具體化結果的兩個篩選器結合在一起 `or` 。 不過，請在每個聯集階段中保留篩選，以保留查詢的語法。
     
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
```
    
