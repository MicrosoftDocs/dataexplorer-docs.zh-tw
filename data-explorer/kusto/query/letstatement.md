---
title: 讓語句 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Let 語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 890a6e21400048031e4ebd3df9749b6c47803e71
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524441"
---
# <a name="let-statement"></a>Let 陳述式

讓語句將名稱綁定到表達式。 對於出現 let 語句的作用域的其餘部分(全域作用域或函數正文作用域中),該名稱可用於引用其綁定值。 如果該名稱以前綁定到另一個值,則使用"最內部"let 語句綁定。

讓語句改進模組化和重用,因為它們允許將可能複雜的表達式分解為多個部分,每個部分通過 let 語句綁定到名稱,並將它們組合在一起。 它們還可用於創建使用者定義的函數和視圖(結果看起來像新表的表的運算式)。

由 let 語句綁定的名稱必須是有效的實體名稱。

由 let 語句的表示式可以是:
* 標量類型
* 表格類型
* 使用者定義的函數(lambdas)

**語法**

`let`*名稱*`=`*標量表示式* | *表格表示式* | *函數定義運算式*

* *名稱*:要綁定的名稱。 名稱必須是有效的實體名稱,並且允許實體名稱轉義(例如`["Name with spaces"]`, )。 
* *ScalarExpression*:具有標量結果的運算式,其值將綁定到名稱。 例如： `let one=1;` 。
* *表格運算式*:具有表面結果的運算式,其值將綁定到名稱。 例如： `Logs | where Timestamp > ago(1h)` 。
* *函數定義表示式*:生成要綁定到名稱的 lambda(匿名函數聲明)的運算式。
  例如： `let f=(a:int, b:string) { strcat(b, ":", a) }` 。

Lambda 表示式具有以下語法:

[`view` `(`] [ ]*表格參數*`,`[ ]*參數*=`)` `{` *函數正文*`}`

`TabularArguments`- [*Tabulararg 名稱*`:``(`]*Atrname* `:` *AtrType*] [`,` ...`)`] [`,` ... ][`,`]

 或: -*表格名稱*`:``(``*``)`】

`ScalarArguments`- [*Argname* `:` *ArgType*] [ ...`,`

* `view`可能只出現在無參數 lambda 中(沒有參數),並指示在查詢時將包含綁定名稱(例如`union *`,使用時)。
* *表格參數*是正式表格表達式參數的清單。
  每個參數有:
  * *表格形式名稱*- 正式表格參數的名稱。 然後,名稱可能出現在*函數體*中,並在調用 lambda 時綁定到特定值。 
  * 表架構定義 - 具有其類型的屬性列表(AtrName : AtrType)。
  lambda 調用中使用的表格表示式必須具有與匹配類型的所有這些屬性,但不限於這些屬性。 
  '(*)' 可用作表格運算式。 在這種情況下,任何表格表達式都可以在 lambda 調用中使用,並且在 lambda 運算式中無法訪問其任何列。
  所有表格參數都應出現在標量參數之前。
* *標參數*是正式標量參數的清單。 
  每個參數有:
  * *ArgName* - 正式標量參數的名稱。 然後,名稱可能出現在*函數體*中,並在調用 lambda 時綁定到特定值。  
  * *ArgType* - 正式標量參數的類型。 目前僅支援以下類型`bool`作為 lambda 參數類型`string``long``datetime``timespan``real``dynamic`:、、、、、、、、 和別名到這些類型。

**多個與嵌套的允許文句**

多個 let 語句可`;`用於它們 之間的分隔符,如以下範例所示。
最後一個語句必須是有效的查詢表示式: 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

允許嵌套 let 語句,可在 lambda 運算式中使用。
let 語句和參數在函數體的當前和內部作用域中可見。

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

**範例**

### <a name="using-let-to-define-constants"></a>使用 let 定義常量

下面的範例將名稱`x`綁定到標量`1`文字 ,然後在表格表示式語句中使用它:

```kusto
let x = 1;
range y from x to x step x
```

同一示例,但在這種情況下 - let 語句`['name']`的名稱使用 概念給出:

```kusto
let ['x'] = 1;
range y from x to x step x
```

還有範例為標量值:

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="using-multiple-let-statements"></a>使用多個 let 語句

下面的範例定義兩個 let 語句,`foo2`其中一個語`foo1`句 ( ) 使用另一個 ( 。

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>使用函數

[`materialize`](materializefunction.md)函數允許在查詢執行期間緩存子查詢結果。 

```kusto
let totalPagesPerDay = PageViews
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
let materializedScope = PageViews
| summarize by Page, Day = startofday(Timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project Page, Day1 = Day
| join kind = inner
(
    cachedResult
    | project Page, Day2 = Day
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1


```

|第 1 天|第 2 天|百分比|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|
