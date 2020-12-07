---
title: Let 陳述式 - Azure 資料總管
description: 本文說明 Azure 資料總管中的 Let 陳述式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.localizationpriority: high
ms.openlocfilehash: c102637adfa1fd0340d28a67b52354956b511ada
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513307"
---
# <a name="let-statement"></a>Let 陳述式

Let 陳述式會將名稱繫結至運算式。 對於剩餘的範圍 (let 陳述式會出現在其中)，名稱可用於參照其繫結的值。 Let 陳述式可能位於全域範圍或函式主體範圍內。
如果該名稱先前已繫結至另一個值，則會使用「最內層」let 陳述式繫結。

Let 陳述式可改善模組化和重複使用，因為其可讓您將可能複雜的運算式分解成多個部分。
每個部分都會透過 let 陳述式繫結至一個名稱，然後一起構成整體。 其也可用來建立使用者定義的函式和檢視。 這些檢視是在結果看起來像新資料表的資料表上的運算式。

> [!NOTE]
> Let 陳述式所繫結的名稱必須是有效的機構名稱。

Let 運算式所繫結的運算式可以是：
* 純量類型
* 表格式類型
* 使用者定義的函式 (lambdas)

## <a name="syntax"></a>語法

`let` *Name* `=` *ScalarExpression* | *TabularExpression* | *FunctionDefinitionExpression*

|欄位  |定義  |範例  |
|---------|---------|---------|
|*名稱*   | 要繫結的名稱。 此名稱必須是有效的實體名稱。    |允許實體名稱逸出，例如 `["Name with spaces"]`。      |
|*ScalarExpression*     |  具有純量結果的運算式，其值會繫結至名稱。  | `let one=1;`  |
|*TabularExpression*    | 具有表格式結果的運算式，其值會繫結至名稱。   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | 可產生 lambda 的運算式，這是要繫結至名稱的匿名函式宣告。   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>Lambda 運算式語法

[`view`] `(`[*TabularArguments*][`,`][*ScalarArguments*]`)` `{` *FunctionBody* `}`

`TabularArguments` - [*TabularArgName* `:` `(`[*AtrName* `:` *AtrType*] [`,` ... ]`)`] [`,` ... ][`,`]

 或者：

 [*TabularArgName* `:` `(` `*` `)`]

`ScalarArguments` - [*ArgName* `:` *ArgType*] [`,` ... ]


|欄位  |定義  |範例  |
|---------|---------|---------|
| **檢視** | 只能出現在沒有引數的無參數 lambda 中。 這表示當「所有資料表」都是查詢時，將會包含繫結名稱。 | 例如，使用 `union *` 時。|
| ***TabularArguments** _ | 正式表格式運算式引數的清單。 
| 每個表格式引數都具有：||
|<ul><li> _TabularArgName*</li></ul> | 正式表格式引數的名稱。 此名稱可能會出現在 *FunctionBody* 中，而且會在叫用 lambda 時繫結至特定值。 ||
|<ul><li>資料表結構描述定義 </li></ul> | 屬性及其類型的清單| AtrName：AtrType|
| ***ScalarArguments** _ | 正式純量引數的清單。 
|每個純量引數都具有：||
|<ul><li>_ArgName*</li></ul> | 正式純量引數的名稱。 此名稱可能會出現在 *FunctionBody* 中，而且會在叫用 lambda 時繫結至特定值。  |
| <ul><li>*ArgType* </li></ul>| 正式純量引數的類型。 | 目前僅支援下列類型作為 lambda 引數類型：`bool`、`string`、`long`、`datetime`、`timespan`、`real` 和 `dynamic` (以及這些類型的別名)。

> [!NOTE]
>Lambda 叫用中使用的表格式運算式必須包含 (但不限於) 具有相符類型的所有屬性。
>
>`(*)` 可作為表格式運算式使用。 
>
> 任何表格式運算式均可使用於 lambda 叫用中，而且在 lambda 運算式中無法存取其任何資料行。 
>
> 所有表格式引數都應該出現在純量引數之前。

## <a name="multiple-and-nested-let-statements"></a>多個和巢狀 let 陳述式

多個 let 陳述式可以搭配分號 `;`、其間的分隔符號使用，如下列範例所示。

> [!NOTE]
> 最後一個陳述式必須是有效的查詢運算式。 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

允許使用巢狀 let 陳述式，而且可以在 lambda 運算式內使用。
Let 陳述式和引數會顯示在函式主體的目前和內部範圍中。

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>範例

### <a name="use-let-function-to-define-constants"></a>使用 let 函式來定義常數

下列範例會將名稱 `x` 繫結至純量常值 `1`，然後在表格式運算式陳述式中加以使用。

```kusto
let x = 1;
range y from x to x step x
```

此範例與前一個範例類似，只有 let 陳述式的名稱會使用 `['name']` 概念來指定。

```kusto
let ['x'] = 1;
range y from x to x step x
```

### <a name="use-let-for-scalar-values"></a>針對純量值使用 let

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>使用 let 陳述式搭配引數進行純量計算

此範例會使用 let 陳述式搭配引數進行純量計算。 此查詢會定義 `MultiplyByN` 函式以便將兩個數字相乘。

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

下列範例會從輸入中移除前置/後置的一 (`1`)。

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


### <a name="use-multiple-let-statements"></a>使用多個 let 陳述式

此範例會定義兩個 let 陳述式，其中一個陳述式 (`foo2`) 會使用另一個 (`foo1`)。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>在 let 陳述式中使用 `view` 關鍵字

此範例會示範如何使用 let 陳述式搭配 `view` 關鍵字。

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


### <a name="use-materialize-function"></a>使用 materialize 函式

[`materialize`](materializefunction.md) 函式可讓您在查詢執行期間快取子查詢結果。 

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
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|
