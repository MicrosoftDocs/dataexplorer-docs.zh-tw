---
title: Let 語句-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Let 語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2e21f0b41f34b469e409109a2586f3e5fd98fa5
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271463"
---
# <a name="let-statement"></a>Let 陳述式

Let 語句會將名稱系結至運算式。 針對出現 let 語句的其餘範圍（全域範圍或在函式主體範圍中），可以使用此名稱來參考其系結值。 如果該名稱先前已系結至另一個值，則會使用「最內層」 let 語句系結。

Let 語句可改善模組化和重複使用，因為它們允許一個將可能複雜的運算式分解成多個部分，每個元件都透過 let 語句系結至名稱，然後一起撰寫。 它們也可以用來建立使用者定義函式和 views （資料表的運算式，其結果看起來就像新的資料表）。

Let 語句所系結的名稱必須是有效的機構名稱。

Let 語句所系結的運算式可以是：
* 純量類型的
* 表格式類型的
* 使用者定義函數（lambda）

**語法**

`let`*名稱* `=`*ScalarExpression*  | *TabularExpression*  | *FunctionDefinitionExpression*

* *名稱*：要系結的名稱。 此名稱必須是有效的機構名稱，而且允許進行機構名稱的轉義（例如 `["Name with spaces"]` ）。 
* *ScalarExpression*：具有純量結果的運算式，其值會系結至名稱。 例如： `let one=1;` 。
* *TabularExpression*：具有表格式結果的運算式，其值會系結至名稱。 例如： `Logs | where Timestamp > ago(1h)` 。
* *FunctionDefinitionExpression*：產生要系結至名稱之 lambda （匿名函式宣告）的運算式。
  例如： `let f=(a:int, b:string) { strcat(b, ":", a) }` 。

Lambda 運算式具有下列語法：

[ `view` ] `(` [*TabularArguments*] [ `,` ] [*ScalarArguments*] `)` `{` *FunctionBody*`}`

`TabularArguments`-[*TabularArgName* `:` `(` [*AtrName* `:` *AtrType*] [ `,` ...] `)` ][`,` ... ][`,`]

 或：-[*TabularArgName* `:` `(` `*` `)` ]

`ScalarArguments`-[*ArgName* `:` *ArgType*] [ `,` ...]

* `view`可能只會出現在無參數 lambda 中（不含任何引數），並指出當「所有資料表」為查詢（例如使用時）時，將會包含系結名稱 `union *` 。
* *TabularArguments*是正式表格式運算式引數的清單。
  每個引數都具有：
  * *TabularArgName* -正式表格式引數的名稱。 然後，名稱可能會出現在*FunctionBody*中，並在叫用 lambda 時系結至特定的值。 
  * 資料表架構定義-具有類型的屬性清單（AtrName： AtrType）。
  Lambda 調用中使用的表格式運算式必須具有所有具有相符類型的屬性，但不限於它們。 
  ' （*） ' 可以當做表格式運算式使用。 在此情況下，任何表格式運算式都可以用在 lambda 調用中，而且它的任何資料行都無法在 lambda 運算式中存取。
  所有表格式引數都應該出現在純量引數之前。
* *ScalarArguments*是正式純量引數的清單。 
  每個引數都具有：
  * *ArgName* -正式純量引數的名稱。 然後，名稱可能會出現在*FunctionBody*中，並在叫用 lambda 時系結至特定的值。  
  * *ArgType* -正式純量引數的類型。 目前僅支援下列類型做為 lambda 引數類型： `bool` 、 `string` 、 `long` 、 `datetime` 、 `timespan` 、 `real` 和 `dynamic` （以及這些類型的別名）。

**多個和嵌套的 let 語句**

多個 let 語句可以與它們之間的分隔符號搭配使用， `;` 如下列範例所示。
最後一個語句必須是有效的查詢運算式： 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

允許使用嵌套的 let 語句，而且可以在 lambda 運算式內使用。
Let 語句和引數會顯示在函式主體的目前和內部範圍中。

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

**範例**

### <a name="using-let-to-define-constants"></a>使用 let 定義常數

下列範例會將名稱系結至純量 `x` 常值 `1` ，然後在表格式運算式語句中使用它：

```kusto
let x = 1;
range y from x to x step x
```

相同的範例，但是在此案例中，let 語句的名稱是使用概念來提供 `['name']` ：

```kusto
let ['x'] = 1;
range y from x to x step x
```

還有另一個使用 let 做為純量值的範例：

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

下列範例會定義兩個 let 語句，其中一個語句（ `foo2` ）會使用另一個（ `foo1` ）。

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>使用具體化函數

[`materialize`](materializefunction.md)函式允許在執行查詢時快取子查詢結果。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|2016-05-01 00：00：00.0000000|2016-05-02 00：00：00.0000000|34.0645725975255|
|2016-05-01 00：00：00.0000000|2016-05-03 00：00：00.0000000|16.618368960101|
|2016-05-02 00：00：00.0000000|2016-05-03 00：00：00.0000000|14.6291376489636|
