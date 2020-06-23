---
title: notbetween 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 notbetween 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 213b69d1458d234e987c8a378ade82441e578d5e
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128609"
---
# <a name="not-between-operator-between"></a>not-between 運算子 (!between)

符合內含範圍以外的輸入。

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between`可以在任何數值、日期時間或 timespan 運算式上操作。
 
**語法**

*T* `|` `where` *expr* `!between` `(` *leftRange* ` .. ` *rightRange*`)`   
 
如果*expr*運算式為 datetime，則會提供另一個語法為的語法：

*T* `|` `where` *expr* `!between` `(` *leftRangeDateTime* ` .. ` *rightRangeTimespan*`)`   

**引數**

* *T* -要比對其記錄的表格式輸入。
* *expr* -要篩選的運算式。
* *leftRange* -左邊範圍（含）的運算式。
* *rightRange* -正確範圍（含）的運算式。

**傳回**

*T*中的資料列（*expr*  <  *leftRange*或*expr*  >  *rightRange*）會評估為 `true` 。

**範例**  

**使用 '！ between ' 運算子篩選數值**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 10 step 1
| where x !between (5 .. 9)
```

|x|
|---|
|1|
|2|
|3|
|4|
|10|

**使用 ' between ' 運算子篩選 datetime**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|58590|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|58590|
