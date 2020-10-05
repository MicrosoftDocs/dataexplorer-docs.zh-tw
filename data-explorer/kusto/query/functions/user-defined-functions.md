---
title: 使用者定義函數-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的使用者定義函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 769ebc16da0780f1d1832dcbf49bad516c47abd3
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712012"
---
# <a name="user-defined-functions"></a>使用者自訂函數

**使用者定義函數** 是可重複使用的子查詢，您可以將其定義為查詢本身的一部分 (臨機操作函 **式**) ，或保存為資料庫中繼資料的一部分 () **儲存的函數** 。 使用者自訂函式是透過 **名稱**來叫用，並提供零個或多個 **輸入引數** (可以是純量或表格式) ，並產生單一值 (可以根據函式 **主體**以純量或表格式) 。

使用者定義函數屬於兩個類別的其中一種：

* 純量函式 
* 表格式函數 

函式的輸入引數和輸出會決定它是純量或表格式，然後建立其使用方式。 

## <a name="scalar-function"></a>純量函數

* 有零個輸入引數，或其所有輸入引數都是純量值
* 產生單一純量值
* 可以在允許純量運算式的任何地方使用
* 可能只會使用定義它的資料列內容
* 只能參考位於可存取架構中的資料表 (和 views) 

## <a name="tabular-function"></a>表格式函數

* 接受一或多個表格式輸入引數，以及零或多個純量輸入引數，以及/或：
* 產生單一表格式值

## <a name="function-names"></a>函數名稱

有效的使用者定義函數名稱必須遵循與其他實體相同的 [識別碼命名規則](../schema-entities/entity-names.md#identifier-naming-rules) 。

名稱在定義的範圍內也必須是唯一的。

## <a name="input-arguments"></a>輸入引數

有效的使用者定義函數會遵循下列規則：

* 使用者自訂函數有零或多個輸入引數的強型別清單。
* 輸入引數具有純量引數的名稱、類型和 () [預設值](#default-values)。
* 輸入引數的名稱是識別碼。
* 輸入引數的類型是其中一種純量資料類型或表格式架構。

在語法上，輸入引數清單是以逗號分隔的引數定義清單（以括弧括住）。 每個引數定義都指定為

```
ArgName:ArgType [= ArgDefaultValue]
```
 針對表格式引數， *ArgType* 具有與資料表定義相同的語法 (括弧和資料行名稱/類型配對的清單) ，另外還支援單獨 `(*)` 表示「任何表格式架構」。

例如：

|語法                        |輸入引數清單描述                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |沒有引數|
|`(s:string)`                  |單一純量引數，名為 `s` 接受型別值 `string`|
|`(a:long, b:bool=true)`       |兩個純量引數，第二個引數有預設值    |
|`(T1:(*), T2(r:real), b:bool)`|三個引數 (兩個表格式引數和一個純量引數)   |

> [!NOTE]
> 使用表格式輸入引數和純量輸入引數時，請將所有表格式輸入引數放在純量輸入引數前面。

## <a name="examples"></a>範例

純量函數：

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

不採用任何引數的表格式函數：

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

同時採用表格式輸入和純量輸入的表格式函數：

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v
};
MyFilter((range x from 1 to 10 step 1), 9)
```

|x|
|---|
|9|
|10|

表格式函數，使用未指定資料行的表格式輸入。
任何資料表都可以傳遞至函式，而且不能在函數內部參考任何資料表資料行。

```kusto
let MyDistinct = (T:(*)) {
  T | distinct *
};
MyDistinct((range x from 1 to 3 step 1))
```

|x|
|---|
|1|
|2|
|3|

## <a name="declaring-user-defined-functions"></a>宣告使用者定義函數

使用者自訂函數的宣告提供：

* 函數 **名稱**
* 函數 **架構** (它接受的參數（如果有的話）) 
* 函數 **主體**

> [!Note]
> 不支援多載函式。 您無法使用相同的名稱和不同的輸入架構來建立多個函數。

> [!TIP]
> Lambda 函數沒有名稱，而且會使用 [let 語句](../letstatement.md)系結至名稱。 因此，它們可以視為使用者定義的預存函數。
> 範例： lambda 函式的宣告，可接受兩個引數 (`string` 呼叫的 `s` 和 `long` 稱為 `i`) 。 它會在將第一個 (轉換成數位之後，傳回第一個的乘積) 第二個。 Lambda 會系結至名稱 `f` ：

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

函數 **主體** 包含：

* 只有一個運算式，它會提供函數的傳回值 (純量或表格式值) 。
* 任一數字 (零或多個 [let 語句](../letstatement.md)) ，其範圍是函數主體的範圍。 如果有指定，let 語句必須在定義函式傳回值的運算式之前。
* 任何數位 (零或多個 [查詢參數語句](../queryparametersstatement.md)) ，其宣告函數所使用的查詢參數。 如果有指定，它們必須在定義函式傳回值的運算式之前。

> [!NOTE]
> 函數主體內不支援查詢「最上層」所支援的其他 [查詢語句](../statements.md) 類型。

### <a name="examples-of-user-defined-functions"></a>使用者定義函數的範例 

**使用 let 語句的使用者定義函數**

下列範例會將名稱系結 `Test` 至使用者自訂函數， (使用三個 let 語句的 lambda) 。 輸出為 `70` ：

```kusto
let Test1 = (id: int) {
  let Test2 = 10;
  let Test3 = 10 + Test2 + id;
  let Test4 = (arg: int) {
      let Test5 = 20;
      Test2 + Test3 + Test5 + arg
  };
  Test4(10)
};
range x from 1 to Test1(10) step 1
| count
```

**定義參數預設值的使用者定義函數**

下列範例顯示可接受三個引數的函式。 後兩個有預設值，而且不一定要存在於呼叫位置。

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>叫用使用者定義函數

不接受任何引數的使用者定義函式可依其名稱或以括弧括住的名稱和空白引數清單來叫用。

範例：

```kusto
// Bind the identifier a to a user-defined function (lambda) that takes
// no arguments and returns a constant of type long:
let a=(){123};
// Invoke the function in two equivalent ways:
range x from 1 to 10 step 1
| extend y = x * a, z = x * a() 
```

```kusto
// Bind the identifier T to a user-defined function (lambda) that takes
// no arguments and returns a random two-by-two table:
let T=(){
  range x from 1 to 2 step 1
  | project x1 = rand(), x2 = rand()
};
// Invoke the function in two equivalent ways:
// (Note that the second invocation must be itself wrapped in
// an additional set of parentheses, as the union operator
// differentiates between "plain" names and expressions)
union T, (T())
```

採用一或多個純量引數的使用者定義函數，可以使用資料表名稱和括弧內的具體引數清單來叫用：

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

使用一或多個資料表引數 (的使用者定義函數，以及任何數目的純量引數) 可以使用資料表名稱和括弧內的具體引數清單來叫用：

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

您也可以使用運算子來叫用 `invoke` 採用一或多個資料表引數並傳回資料表的使用者定義函數。 當函式的第一個具象資料表引數是運算子的來源時，此函數會很有用 `invoke` ：

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>預設值

在下列情況下，函式可能會提供預設值給其部分參數：

* 只有純量參數可以提供預設值。
* 預設值一律是常值 (常數) 。 它們不能是任意計算。
* 沒有預設值的參數一律在具有預設值的參數之前。
* 呼叫端必須提供所有參數的值，而且沒有預設值的相片順序與函式宣告的順序相同。
* 呼叫端不需要提供具有預設值的參數值，但可能會這樣做。
* 呼叫端可能會以不符合參數順序的順序提供引數。 如果是，它們必須命名其引數。

下列範例會傳回具有兩筆相同記錄的資料表。 在的第一個調用中 `f` ，引數會完全「經過編碼」，因此會明確指定每個引數的名稱：

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
union
  (print x=f(c=7, a=12)), // "12-b.default-7"
  (print x=f(12, c=7))    // "12-b.default-7"
```

|x|
|---|
|12-b. 預設值-7|
|12-b. 預設值-7|

## <a name="view-functions"></a>View 函數

未採用任何引數並傳回表格式運算式的使用者自訂函數，可以標示為 **view**。 將使用者自訂函數標示為 view，表示每當完成萬用字元資料表名稱解析時，函數的行為就像資料表。
下列範例顯示兩個使用者定義的函式，以及 `T_view` `T_notview` 顯示中的萬用字元參考如何解析第一個函數 `union` 。

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>限制

適用下列限制：

* 使用者自訂函數無法傳遞至 [toscalar ( # B1 ](../toscalarfunction.md) 調用資訊，其相依于呼叫函式的資料列內容。
* 傳回表格式運算式的使用者自訂函數，不能以與資料列內容不同的引數叫用。
* 無法在遠端叢集上叫用至少接受一個表格式輸入的函數。
* 無法在遠端叢集上叫用純量函數。

只有當使用者定義函數是由純量函陣列成，而且不使用時，才能使用與資料列內容不同的引數來叫用使用者定義函數 `toscalar()` 。

**限制1的範例**

```kusto
// Supported:
// f is a scalar function that doesn't reference any tabular expression
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { now() + hours*1h };
Table2 | where Column != 123 | project d = f(10)

// Supported:
// f is a scalar function that references the tabular expression Table1,
// but is invoked with no reference to the current row context f(10):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(10)

// Not supported:
// f is a scalar function that references the tabular expression Table1,
// and is invoked with a reference to the current row context f(Column):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(Column)
```

**限制2的範例**

```kusto
// Not supported:
// f is a tabular function that is invoked in a context
// that expects a scalar value.
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { range x from 1 to hours step 1 | summarize make_list(x) };
Table2 | where Column != 123 | project d = f(Column)
```

## <a name="features-that-are-currently-unsupported-by-user-defined-functions"></a>使用者定義函數目前不支援的功能

為求完整性，以下是目前不支援的使用者定義函數的一些常用功能：

1.  函數多載：目前沒有任何方法可以多載函式 (例如，建立多個具有相同名稱和不同輸入架構的函式) 。

2.  預設值：函式純量參數的預設值必須是純量常值 (常數) 。 此外，預存函式不能有類型的預設值 `dynamic` 。
