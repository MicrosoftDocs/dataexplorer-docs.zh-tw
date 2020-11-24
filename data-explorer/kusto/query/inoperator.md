---
title: in 和 notin 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的和 notin 操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.localizationpriority: high
ms.openlocfilehash: ffb24abe744bfbe3f7f95336edf0263becfa7ec9
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513245"
---
# <a name="in-and-in-operators"></a>in 和 !in 運算子

根據提供的值集篩選記錄集。

```kusto
Table1 | where col in ('value1', 'value2')
```

> [!NOTE]
> * 將 ' ~ ' 新增至運算子會讓值的搜尋不區分大小寫： `x in~ (expression)` 或 `x !in~ (expression)` 。
> * 在表格式運算式中，會選取結果集的第一個資料行。
> * 運算式清單可以產生最多 `1,000,000` 值。
> * 嵌套陣列會壓平合併成單一值清單。 例如，`x in (dynamic([1,[2,3]]))` 會成為 `x in (1,2,3)`。
 
## <a name="syntax"></a>語法

### <a name="case-sensitive-syntax"></a>區分大小寫語法

*T* 純 `|` `where` *col* `in` `(` *量運算式的* T 欄清單`)`   
*T* `|` `where` *col* `in` `(` *表格式運算式*`)`   
 
*T* 純 `|` `where` *col* `!in` `(` *量運算式的* T 欄清單`)`  
*T* `|` `where` *col* `!in` `(` *表格式運算式*`)`   

### <a name="case-insensitive-syntax"></a>不區分大小寫語法

*T* 純 `|` `where` *col* `in~` `(` *量運算式的* T 欄清單`)`   
*T* `|` `where` *col* `in~` `(` *表格式運算式*`)`   
 
*T* 純 `|` `where` *col* `!in~` `(` *量運算式的* T 欄清單`)`  
*T* `|` `where` *col* `!in~` `(` *表格式運算式*`)`   

## <a name="arguments"></a>引數

* *T* -要篩選其記錄的表格式輸入。
* *col* -要篩選的資料行。
* *運算式清單* -表格式、純量或常值運算式的逗號分隔清單。
* *表格式運算式* -具有一組值的表格式運算式。 如果運算式有多個資料行，則會使用第一個資料行。

## <a name="returns"></a>傳回

述詞為 *T* 中的資料列 `true` 。

## <a name="examples"></a>範例  

### <a name="use-in-operator"></a>使用 ' in ' 運算子

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|計數|
|---|
|4775|  

### <a name="use-in-operator"></a>使用 ' in ~ ' 運算子  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|計數|
|---|
|4775|  

### <a name="use-in-operator"></a>使用 '！ in ' 運算子

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|計數|
|---|
|54291|  


### <a name="use-dynamic-array"></a>使用動態陣列

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|計數|
|---|
|3218|

### <a name="subquery"></a>子查詢

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Using subquery
let Top_5_States = 
StormEvents
| summarize count() by State
| top 5 by count_; 
StormEvents 
| where State in (Top_5_States) 
| count
```

您可以撰寫相同的查詢，如下所示：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Inline subquery 
StormEvents 
| where State in (
    ( StormEvents
    | summarize count() by State
    | top 5 by count_ )
) 
| count
```

|計數|
|---|
|14242|  

### <a name="top-with-other-example"></a>Top 與其他範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| State     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| 威斯康辛州 | 31                   |
| 德克薩斯州     | 55                   |
| 佛羅里達   | 85                   |
| 格魯吉亞   | 106                  |
| 其他     | 415                  |

### <a name="use-a-static-list-returned-by-a-function"></a>使用函數所傳回的靜態清單

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|計數|
|---|
|4775|  

函式定義。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.show function InterestingStates
```

|Name|參數|主體|資料夾|DocString|
|---|---|---|---|---|
|InterestingStates|()|{dynamic ( [「華盛頓州」、「佛羅里達州」、「南喬治亞」、「紐約」 ) }
