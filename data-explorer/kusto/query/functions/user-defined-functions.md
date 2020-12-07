---
title: 使用者定義的函式 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中使用者定義的函式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: 92627b3325a7a2ba8e2e4d58a82ebf6db3977221
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512871"
---
# <a name="user-defined-functions"></a>使用者自訂函數

**使用者定義的函式** 是可重複使用的子查詢，其可定義為查詢本身的一部分 (**特殊函式**)，或保存為資料庫中繼資料的一部分 (**預存函式**)。 使用者定義的函式會透過 **名稱** 叫用、獲得零個或多個 **輸入引數** (可以是純量或表格式)，並根據函式 **主體** 產生單一值 (可以是純量或表格式)。

使用者定義的函式屬於下列兩個類別之一：

* 純量函式 
* 表格式函式 

函式的輸入引數和輸出可決定其為純量或表格式，據以建立其使用方式。 

## <a name="scalar-function"></a>純量函數

* 有零個輸入引數，或其所有輸入引數都是純量值
* 產生單一純量值
* 可以在允許使用純量運算式的情況下使用
* 只能使用其定義所在的資料列內容
* 只能參考位於可存取結構描述中的資料表 (和檢視)

## <a name="tabular-function"></a>表格式函式

* 接受一或多個表格式輸入引數，及零個或多個純量輸入引數，以及/或：
* 產生單一表格式值

## <a name="function-names"></a>函式名稱

有效的使用者定義函式名稱必須遵循與其他實體相同的[識別碼命名規則](../schema-entities/entity-names.md#identifier-naming-rules)。

名稱在其定義範圍中也必須是唯一的。

> [!NOTE]
> 如果預存函式和資料表有相同的名稱，則在查詢資料表/函式名稱時，預存函式會覆寫。

## <a name="input-arguments"></a>輸入引數

有效的使用者定義函式會遵循下列規則：

* 使用者定義的函式具有零個或多個輸入引數的強型別清單。
* 輸入引數具有名稱、類型和 (適用於純量引數) [預設值](#default-values)。
* 輸入引數的名稱是識別碼。
* 輸入引數的類型是其中一種純量資料類型或表格式結構描述。

在語法上，輸入引數清單是以逗號分隔的引數定義清單 (以括號括住)。 每個引數定義都會指定為

```
ArgName:ArgType [= ArgDefaultValue]
```
 對於表格式引數，*ArgType* 具有與資料表定義 (括弧和資料行名稱/類型組清單) 相同的語法，並提供單獨 `(*)` 的額外支援，以指出「任何表格式結構描述」。

例如：

|語法                        |輸入引數清單描述                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |無引數|
|`(s:string)`                  |稱為 `s` 並可接受 `string` 類型值的單一純量引數|
|`(a:long, b:bool=true)`       |兩個純量引數，第二個具有預設值    |
|`(T1:(*), T2(r:real), b:bool)`|三個引數 (兩個表格式引數和一個純量引數)  |

> [!NOTE]
> 同時使用表格式輸入引數和純量輸入引數時，請將所有表格式輸入引數放在純量輸入引數之前。

## <a name="examples"></a>範例

純量函式：

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

不接受引數的表格式函式：

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

同時接受表格式輸入和純量輸入的表格式函式：

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

表格式函式，其使用未指定資料行的表格式輸入。
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

## <a name="declaring-user-defined-functions"></a>宣告使用者定義的函式

使用者定義函式的宣告可提供：

* 函式 **名稱**
* 函式 **結構描述** (如果有的話，其接受的參數)
* 函式 **主體**

> [!Note]
> 不支援多載函式。 您無法建立多個具有相同名稱和不同輸入結構描述的函式。

> [!TIP]
> Lambda 函式沒有名稱，而且會使用 [let 陳述式](../letstatement.md)繫結至名稱。 因此，可以將其視為使用者定義的預存函式。
> 範例：Lambda 函式的宣告，可接受兩個引數 (稱為 `s` 的 `string`，以及稱為 `i` 的 `long`)。 其會傳回第一個引數 (將其轉換為數字之後) 與第二個引數的產物。 Lambda 會繫結至名稱 `f`：

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

函式 **主體** 包含：

* 正好一個運算式，其可提供函式的傳回值 (純量或表格式值)。
* 任意數量 (零個或多個) 的 [let 陳述式](../letstatement.md)，其範圍是函式主體的範圍。 若已指定，let 陳述式必須在定義函式傳回值的運算式前面。
* 任意數量 (零個或多個) 的[query parameters 陳述式](../queryparametersstatement.md)，其可宣告函式所使用的查詢參數。 若已指定，其必須在定義函式傳回值的運算式前面。

> [!NOTE]
> 在函式主體內，不支援查詢「最上層」所支援的其他種類[查詢陳述式](../statements.md)。

### <a name="examples-of-user-defined-functions"></a>使用者定義的函式範例 

**使用 let 陳述式的使用者定義函式**

下列範例會將名稱 `Test` 繫結至使用三個 let 陳述式的使用者定義函式 (lambda)。 輸出為 `70`：

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

**可定義參數預設值的使用者定義函式**

下列範例顯示可接受三個引數的函式。 後面兩個引數有預設值，而且不必存在於呼叫位置。

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>叫用使用者定義的函式

不接受引數的使用者定義函式，可以經由其名稱，或經由其名稱和以括弧括住的空引數清單來叫用。

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

接受一或多個純量引數的使用者定義函式，可藉由使用資料表名稱和以括弧括住的具體引數清單來叫用：

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

接受一或多個資料表引數的使用者定義函式 (以及任何數目的純量引數)，可以使用資料表名稱和以括弧括住的具體引數清單來叫用：

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

您也可以使用 `invoke` 運算子來叫用可接受一或多個資料表引數並傳回資料表的使用者定義函式。 當函式的第一個具體資料表引數是 `invoke` 運算子的來源時，此函式很有用：

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>預設值

在下列情況下，函式可能會提供其某些參數的預設值：

* 只會對純量參數提供預設值。
* 預設值一律為常值 (常數)。 不能是任意計算。
* 沒有預設值的參數一律在具有預設值的參數前面。
* 呼叫端必須提供所有參數的值，且預設值不會以與函式宣告相同的順序排列。
* 呼叫端不需要提供具有預設值的參數值，但可能會這麼做。
* 呼叫端可能會以不符合參數順序的順序來提供引數。 若是如此，就必須為其引數命名。

下列範例會傳回具有兩筆相同記錄的資料表。 在 `f` 的第一次叫用中，引數會完全「變碼」，因此每一筆記錄都會有明確指定的名稱：

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
|12-b.default-7|
|12-b.default-7|

## <a name="view-functions"></a>檢視函式

不接受引數並傳回表格式運算式的使用者定義函式，可以標示為 **view**。 將使用者定義的函式標示為 view，表示每當完成萬用字元資料表名稱解析時，函數的作用就像資料表。
下列範例顯示兩個使用者定義的函式 (`T_view` 和 `T_notview`)，並顯示 `union` 中的萬用字元參考如何只解析第一個函式：

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>限制

適用以下限制：

* 使用者定義的函式不能傳入 [toscalar()](../toscalarfunction.md) 叫用資訊，其取決於函式呼叫所在的資料列內容。
* 不能使用隨著資料列內容變化的引數，叫用可傳回表格式運算式的使用者定義函式。
* 無法在遠端叢集上叫用至少接受一個表格式輸入的函式。
* 無法在遠端叢集上叫用純量函式。

只有當使用者定義的函式僅由純量函式組成且未使用 `toscalar()` 時，才能使用會隨著資料列內容變化的引數叫用使用者定義的函式。

**限制 1 的範例**

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

**限制 2 的範例**

```kusto
// Not supported:
// f is a tabular function that is invoked in a context
// that expects a scalar value.
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { range x from 1 to hours step 1 | summarize make_list(x) };
Table2 | where Column != 123 | project d = f(Column)
```

## <a name="features-that-are-currently-unsupported-by-user-defined-functions"></a>使用者定義的函式目前不支援的功能

為求完整性，以下是目前不支援的使用者定義函式的一些常要求功能：

1.  函式多載：目前沒有任何方法可以多載函式 (亦即，建立多個具有相同名稱和不同輸入結構描述的函數)。

2.  預設值：函式的純量參數預設值必須是純量常值 (常數)。 此外，預存函式不能有 `dynamic` 類型的預設值。
