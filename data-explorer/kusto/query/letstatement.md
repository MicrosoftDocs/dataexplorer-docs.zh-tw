---
title: Let 語句-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Let 語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.localizationpriority: high
ms.openlocfilehash: c102637adfa1fd0340d28a67b52354956b511ada
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513307"
---
# <a name="let-statement"></a>Let 陳述式

Let 語句會將名稱系結至運算式。 針對範圍的其餘部分（其中會出現 let 語句），名稱可以用來參考其系結值。 Let 語句可能在全域範圍或函式主體範圍內。
如果該名稱先前已系結至其他值，則會使用「最內層」 let 語句系結。

Let 語句可改善模組化並重複使用，因為它們可讓您將可能複雜的運算式分解成多個部分。
每個部分都會透過 let 語句系結至名稱，並同時構成整個。 它們也可以用來建立使用者定義的函數和視圖。 這些視圖是資料表的運算式，其結果看起來就像新的資料表。

> [!NOTE]
> Let 語句所系結的名稱必須是有效的機構名稱。

Let 語句所系結的運算式可以是：
* 純量類型
* 表格式類型
*  (lambda 的使用者定義函數) 

## <a name="syntax"></a>語法

`let`*名稱* `=`*ScalarExpression*  | *TabularExpression*  | *FunctionDefinitionExpression*

|欄位  |定義  |範例  |
|---------|---------|---------|
|*名稱*   | 要繫結的名稱。 名稱必須是有效的機構名稱。    |允許對機構名稱進行轉義，例如 `["Name with spaces"]` ，。      |
|*ScalarExpression*     |  具有純量結果的運算式，其值會系結至名稱。  | `let one=1;`  |
|*TabularExpression*    | 具有表格式結果的運算式，其值會系結至名稱。   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | 產生 lambda 的運算式，這是要系結至名稱的匿名函式宣告。   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>Lambda 運算式語法

[ `view` ] `(` [*TabularArguments*] [ `,` ] [*ScalarArguments*] `)` `{` *FunctionBody*`}`

`TabularArguments` -[*TabularArgName* `:` `(` [*AtrName* `:` *AtrType*] [ `,` ...] `)` ][`,` ... ][`,`]

 或者：

 [*TabularArgName* `:` `(``*` `)`]

`ScalarArguments` -[*ArgName* `:` *ArgType*] [ `,` ...]


|欄位  |定義  |範例  |
|---------|---------|---------|
| **檢視** | 可能只會出現在沒有引數的無參數 lambda 中。 這表示當「所有資料表」是查詢時，將會包含系結名稱。 | 例如，在使用時 `union *` 。|
| ***TabularArguments** _ | 正式表格式運算式引數的清單。 
| 每個表格式引數都有：||
|<ul><li> _TabularArgName *</li></ul> | 正式表格式引數的名稱。 名稱可能會出現在 *FunctionBody* 中，並在叫用 lambda 時系結至特定的值。 ||
|<ul><li>資料表架構定義 </li></ul> | 具有類型的屬性清單| AtrName : AtrType|
| ***ScalarArguments** _ | 正式純量引數的清單。 
|每個純量引數都有：||
|<ul><li>_ArgName *</li></ul> | 正式純量引數的名稱。 名稱可能會出現在 *FunctionBody* 中，並在叫用 lambda 時系結至特定的值。  |
| <ul><li>*ArgType* </li></ul>| 正式純量引數的型別。 | 目前僅支援下列類型做為 lambda 引數類型： `bool` 、 `string` 、 `long` 、 `datetime` 、 `timespan` 、 `real` 和 `dynamic` (以及這些類型的別名) 。

> [!NOTE]
>Lambda 調用中使用的表格式運算式必須包含 (但不限於) 所有具有相符類型的屬性。
>
>`(*)` 可以當做表格式運算式使用。 
>
> 任何表格式運算式都可以用於 lambda 調用中，而且無法在 lambda 運算式中存取其任何資料行。 
>
> 所有表格式引數應該出現在純量引數前面。

## <a name="multiple-and-nested-let-statements"></a>Multiple 和 nested let 語句

您可以使用多個 let 語句搭配分號、 `;` 分隔符號之間的分隔符號，如下列範例所示。

> [!NOTE]
> 最後一個語句必須是有效的查詢運算式。 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

允許嵌套 let 語句，而且可以在 lambda 運算式內使用。
在函式主體的目前和內部範圍中，可以看到 Let 語句和引數。

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>範例

### <a name="use-let-function-to-define-constants"></a>使用 let 函數來定義常數

下列範例會將名稱系結至純量 `x` 常值 `1` ，然後在表格式運算式語句中使用它。

```kusto
let x = 1;
range y from x to x step x
```

這個範例與上一個範例類似，只有 let 語句的名稱是使用概念來指定 `['name']` 。

```kusto
let ['x'] = 1;
range y from x to x step x
```

### <a name="use-let-for-scalar-values"></a>使用 let 作為純量值

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>搭配使用 let 語句與純量計算的引數

此範例會使用包含純量計算引數的 let 語句。 查詢會定義將 `MultiplyByN` 兩個數字相乘的函數。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let MultiplyByN = (val:long, n:long) { val * n };
range x from 1 to 5 step 1 
| extend result = MultiplyByN(x, 5)
```

|x|result|
|---|---|
|1|5|
|2|10|
|3|15|
|4|20|
|5|25|

下列範例會從輸入中移除前置/尾端 `1` 的 () 。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let TrimOnes = (s:string) { trim("1", s) };
range x from 10 to 15 step 1 
| extend result = TrimOnes(tostring(x))
```

|x|result|
|---|---|
|10|0|
|11||
|12|2|
|13|3|
|14|4|
|15|5|


### <a name="use-multiple-let-statements"></a>使用多個 let 語句

這個範例會定義兩個 let 語句，其中一個語句 (`foo2`) 使用另一個 (`foo1`) 。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>`view`在 let 語句中使用關鍵字

此範例示範如何搭配使用 let 語句與 `view` 關鍵字。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Range10 = view () { range MyColumn from 1 to 10 step 1 };
let Range20 = view () { range MyColumn from 1 to 20 step 1 };
search MyColumn == 5
```

|$table|MyColumn|
|---|---|
|Range10|5|
|Range20|5|


### <a name="use-materialize-function"></a>使用具體化函數

[`materialize`](materializefunction.md)函數可讓您在查詢執行期間快取子查詢結果。 

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
