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
ms.openlocfilehash: e9e199631d57f3fd3be8d438e6b0cdbf9bdd4266
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227354"
---
# <a name="user-defined-functions"></a>使用者自訂函數

**使用者定義函數**是可重複使用的子查詢，可以定義為查詢本身的一部分（臨機操作**函數**），或保存為資料庫中繼資料（**預存**函式）的一部分。 使用者定義函數是透過**名稱**來叫用，提供零個或多個**輸入引數**（可以是純量或表格式），並根據函式**主體**產生單一值（可以是純量或表格式）。

使用者定義函數屬於兩個類別的其中一個：

* 純量函式 
* 表格式函數 

函式的輸入引數和輸出會判斷它是純量或表格式，接著會建立它的使用方式。 

## <a name="scalar-function"></a>純量函數

* 有零個輸入引數，或其所有輸入引數都是純量值
* 產生單一純量值
* 可以在允許純量運算式的任何位置使用
* 只能使用其定義所在的資料列內容
* 只能參考位於可存取的架構中的資料表（和 views）

## <a name="tabular-function"></a>表格式函數

* 接受一或多個表格式輸入引數，以及零個或多個純量輸入引數，以及/或：
* 產生單一表格式值

## <a name="function-names"></a>函數名稱

有效的使用者定義函數名稱必須遵循與其他實體相同的[識別碼命名規則](../schema-entities/entity-names.md#identifier-naming-rules)。

名稱在其定義範圍中也必須是唯一的。

> [!NOTE]
> 不支援函數多載。 您無法使用相同的名稱來定義多個函數。

## <a name="input-arguments"></a>輸入引數

有效的使用者定義函數會遵循下列規則：

* 使用者定義函數具有零或多個輸入引數的強型別清單。
* 輸入引數具有名稱、類型和（純量引數）[預設值](#default-values)。
* 輸入引數的名稱是識別碼。
* 輸入引數的類型是其中一個純量資料類型或表格式架構。

在語法上，輸入引數清單是以括弧括住的引數定義清單（以逗號分隔）。 每個引數定義都指定為

```
ArgName:ArgType [= ArgDefaultValue]
```
 對於表格式引數， *ArgType*具有與資料表定義相同的語法（括弧和資料行名稱/類型配對的清單），並具有單獨的額外支援， `(*)` 表示「任何表格式架構」。

例如：

|語法                        |輸入引數清單描述                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |沒有引數|
|`(s:string)`                  |名為的單一純量引數， `s` 接受類型的值`string`|
|`(a:long, b:bool=true)`       |兩個純量引數，第二個具有預設值    |
|`(T1:(*), T2(r:real), b:bool)`|三個引數（兩個表格式引數和一個純量引數）  |

> [!NOTE]
> 同時使用表格式輸入引數和純量輸入引數時，請將所有表格式輸入引數放在純量輸入引數之前。

## <a name="examples"></a>範例

純量函數：

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

不接受引數的表格式函數：

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

同時接受表格式輸入和純量輸入的表格式函數：

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
任何資料表都可以傳遞至函式，而且不能在函式內參考任何資料表資料行。

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

使用者定義函數的宣告提供：

* 函數**名稱**
* 函式**架構**（它接受的參數，如果有的話）
* 函式**主體**

> [!Note]
> 不支援多載函式。 您無法使用相同的名稱和不同的輸入架構來建立多個函數。

> [!TIP]
> Lambda 函數沒有名稱，而且會使用[let 語句](../letstatement.md)系結至名稱。 因此，它們可以被視為使用者定義的預存函數。
> 範例： lambda 函式的宣告，該函式會接受兩個引數（名為的 `string` `s` 和名為的 `long` `i` ）。 它會傳回第一個的乘積（將它轉換成數位之後）和第二個。 Lambda 會系結至名稱 `f` ：

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

函數**主體**包含：

* 只有一個運算式，它會提供函數的傳回值（純量或表格式值）。
* [Let 語句](../letstatement.md)的任何數目（零或多個），其範圍是函式主體的範圍。 如果指定，let 語句必須在定義函式傳回值的運算式前面。
* [查詢參數語句](../queryparametersstatement.md)的任何數目（零或多個），其宣告函式所使用的查詢參數。 若已指定，則必須在定義函式傳回值的運算式前面。

> [!NOTE]
> 在「最上層」查詢中支援的其他類型[查詢語句](../statements.md)，在函式主體內不受支援。

### <a name="examples-of-user-defined-functions"></a>使用者定義函數的範例 

**使用 let 語句的使用者定義函數**

下列範例會將名稱系 `Test` 結至使用三個 let 語句的使用者定義函數（lambda）。 輸出為 `70` ：

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

下列範例顯示接受三個引數的函式。 後面兩個有預設值，而且不需要出現在呼叫位置。

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>叫用使用者自訂函數

不接受引數的使用者定義函數，可以透過其名稱或其名稱，或以括弧括住的空引數清單來叫用。

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

採用一或多個純量引數的使用者自訂函數，可以使用資料表名稱和以括弧括住的具體引數清單來叫用：

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

接受一個或多個資料表引數的使用者自訂函數（以及任何數目的純量引數），可以使用資料表名稱和以括弧括住的具體引數清單來叫用：

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

您也可以使用運算子，叫用 `invoke` 接受一或多個資料表引數並傳回資料表的使用者自訂函數。 當函式的第一個具體資料表引數是運算子的來源時，此函數會很有用 `invoke` ：

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>預設值

函式可能會在下列情況下，提供其某些參數的預設值：

* 只有純量參數可以提供預設值。
* 預設值一律為常值（常數）。 它們不能是任意計算。
* 沒有預設值的參數一律會在具有預設值的參數前面。
* 呼叫端必須提供所有參數的值，且沒有預設值與函式宣告的順序排列。
* 呼叫端不需要提供具有預設值的參數值，但可能會這麼做。
* 呼叫端可能會以不符合參數順序的順序來提供引數。 若是如此，它們必須命名其引數。

下列範例會傳回包含兩個相同記錄的資料表。 在的第一次叫用 `f` 時，引數完全是「已編碼」，因此每一個都明確指定一個名稱：

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
|12-b。預設值-7|
|12-b。預設值-7|

## <a name="view-functions"></a>View 函數

不接受引數並傳回表格式運算式的使用者定義函數，可以標示為**view**。 將使用者自訂函數標示為 view，表示每當完成萬用字元資料表名稱解析時，函數的行為就像資料表。
下列範例顯示兩個使用者定義函數 `T_view` 和 `T_notview` ，並顯示在中，萬用字元參考如何解析第一個函式 `union` ：

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>限制

適用以下限制：

* 使用者定義函式無法傳遞到[toscalar （）](../toscalarfunction.md)調用資訊，這會相依于呼叫函數的資料列內容。
* 傳回表格式運算式 can'tbe 的使用者自訂函數，其以與資料列內容不同的引數叫用。
* 至少接受一個表格式輸入的函式無法在遠端叢集上叫用。
* 無法在遠端叢集上叫用純量函數。

唯一的使用者定義函數可以使用與資料列內容不同的引數來叫用，這是由使用者定義函式僅由純量函式所組成，而不是使用 `toscalar()` 。

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
