---
title: 中的和非運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的運算符和運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: bd247de2bd211ae7be3da449e940899d2e8bb475
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513799"
---
# <a name="in-and-in-operators"></a>in 和 !in 運算子

根據提供的值集篩選記錄集。

```kusto
Table1 | where col in ('value1', 'value2')
```

**語法**

*區分大小寫的語法:*

*標量表示式的**T* `|` `where` *col*`in``(`清單`)`   
*T* `where` `(`T *col* col*表格表示式*`|``in``)`   
 
*標量表示式的**T* `|` `where` *col*`!in``(`清單`)`  
*T* `where` `(`T *col* col*表格表示式*`|``!in``)`   

*不區分大小寫的語法:*

*標量表示式的**T* `|` `where` *col*`in~``(`清單`)`   
*T* `where` `(`T *col* col*表格表示式*`|``in~``)`   
 
*標量表示式的**T* `|` `where` *col*`!in~``(`清單`)`  
*T* `where` `(`T *col* col*表格表示式*`|``!in~``)`   

**引數**

* *T* - 要篩選其記錄的表格輸入。
* *col* - 要篩選的欄。
* *表示式清單*─表格、標量或文字表示式逗號分隔清單  
* *表格表示式*─ 具有一組值的表格表示式(在案例運算式中有多個欄,使用第一欄)

**傳回**

謂詞為*T*中的行`true`

**注意事項**

* 表示式清單可以產生最多`1,000,000`的值    
* 巢狀陣列被拼成單個值清單,例如`x in (dynamic([1,[2,3]]))`轉換為`x in (1,2,3)` 
* 對於表格表示式,選擇結果集的第一列   
* 將運算子加入「*」會使值的搜尋大小寫不敏感`x in~ (expression)`:`x !in~ (expression)`或 。

**範例：**  

**"in"運算子的簡單用法:**  

```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|4775|  


**"in+"運算子的簡單用法:**  

```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Count|
|---|
|4775|  

**"!in"運算子的簡單用法:**  

```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|54291|  


**使用動態陣列:**
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|Count|
|---|
|3218|


**子查詢範例:**  

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

相同的查詢可以編寫為:

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

|Count|
|---|
|14242|  

**其他範例的頂部:**  

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

**使用函數傳回的靜態清單:**  

```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Count|
|---|
|4775|  


下面是函數定義:  

```kusto
.show function InterestingStates
```

|名稱|參數|body|資料夾|文件字串|
|---|---|---|---|---|
|有趣的國家|()|[ 動態的("華盛頓","佛羅里達","喬治亞","紐約"*)
