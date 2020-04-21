---
title: 使用者定義的函數 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中使用者定義的函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e8372be303b1540e1421f226ed0fd0fe74d44545
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610246"
---
# <a name="user-defined-functions"></a>使用者定義的函式

Kusto 支援使用者定義的函數,這些函數可以是:
* 查詢本身的一部份(**暫時函數**) 
* 為資料庫中繼資料的一部份以持久方式儲存 (**儲存的函數**)

使用者定義的函數具有:
* 名稱:
    * 必須遵循[識別子命名規則](../schema-entities/entity-names.md#identifier-naming-rules)
    * 在具有類型規範的定義範圍內必須是唯一的
* 強型別輸入參數清單:
    * 可以是標量表示式或表格運算式
    * Scalar 參數可能提供預設值,當函數的呼叫者不提供參數的值時隱式使用(請參考下面的[預設值](#default-values))
* 強類型傳回值,可以是標量值或表格

函數的輸入和輸出決定如何使用與位置:

* **標量函數**: 
    * 是一個沒有輸入的函數,或者僅具有標量輸入,並且產生標量輸出
    * 讓您可以讓標準標示表示的任何位置使用
    * 只能使用定義它的列中文
    * 只能參考可存取架構中的表(與檢視)

範例：

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

* **表格函數**: 
    * 是一個沒有輸入或至少一個表格輸入的函數,並產生表格輸出
    * 讓您可以讓表格表示的任何位置使用 

> [!NOTE]
> 所有表格參數必須出現在標量參數之前。

不需要參數的表格函式範例:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

使用表格輸入和標量輸入的表格函式範例:

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

使用未指定列的表格輸入的表格函數的範例。 任何表都可以傳遞給函數,並且不能在函數內引用任何表的列。

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

## <a name="declaring-user-defined-functions"></a>宣告使用者定義的函數

使用者定義的函數的聲明提供:

* 函數**名稱**
* 函數**架構**(它接受的參數(如果有)
* 功能**body**

> [!Note]
> 不支援重載功能。 例如,不能創建具有相同名稱和不同輸入架構的多個函數。

> [!TIP]
> Lambda 函數沒有名稱,並且使用[let 語句](../letstatement.md)綁定到名稱。 因此,它們可以被視為使用者定義的存儲函數。
> 範例:接受兩個`string`參數(`s`稱為和呼`long``i`叫的)的lambda函數的聲明。 它返回第一個(將其轉換為數位)和第二個產品的產品。 lambda 繫結為`f`名稱 :

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

功能**內文**包括:

* 正好一個表達式,它提供函數的返回值(標段或表格值)。
* [let 語句](../letstatement.md)的任意數位(零或更多),其範圍是函數體的範圍。 如果指定,let 語句必須在定義函數的返回值的表達式之前。
* [查詢參數語句](../queryparametersstatement.md)的任意數量(零或更多),用於聲明函數使用的查詢參數。 如果指定,它們必須位於定義函數的返回值的運算式之前。

> [!NOTE]
> 在查詢「頂層」中支援的其他類型的[查詢語句](../statements.md)在函數體內不受支援。

### <a name="examples-of-user-defined-functions"></a>使用者定義的函數範例 

**使用 let 語片的使用者定義函式**

下面的範例將名稱`Test`綁定到使用者定義的函數 (lambda),該函數使用三個 let 語句。 輸出為`70`:

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

**定義參數預設值的使用者定義的函數**

下面的示例顯示了一個接受三個參數的函數。 后兩個具有預設值,不必出現在調用網站中。

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>呼叫使用者定義的函數

不需要參數的使用者定義的函數可以由其名稱或其名稱調用,括弧中為空參數清單。

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

可以使用表名與括號中的參數清單呼叫以採用一個或多個標量參數的使用者定義的函數:

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

可以使用表格名稱和括號中的參數清單呼叫以採用一個或多個表參數(與任意數量的標量參數)的使用者定義的函數:

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

您還可以使用 運算子`invoke`調用使用者定義的函數,該函數採用一個或多個表參數並返回表。 當函數的第一個具體表參數是運算子的源時,`invoke`這非常有用:

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>預設值

在以下情況下,函數可能會為其某些參數提供預設值:

* 只能為標量參數提供預設值。
* 默認值始終是文本(常量)。 它們不能是任意計算。
* 沒有預設值的參數始終位於具有預設值的參數之前。
* 調用方必須提供所有參數的值,沒有按與函數聲明相同的順序排列的預設值。
* 調用方不需要為具有預設值的參數提供值,但可能會這樣做。
* 調用方可能按與參數順序不匹配的順序提供參數。 如果是這樣,他們必須說出他們的論點。

下面的示例返回具有兩個相同記錄的表。 在第一個調用`f`中 ,參數完全「混亂」,因此每個參數都顯式指定一個名稱:

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
|12-b.預設-7|
|12-b.預設-7|

## <a name="view-functions"></a>檢視函數

使用者定義的函數不需要參數並傳回表格表示式,可以標記為**檢視**。 將使用者定義的函數標記為視圖意味著,每當完成通配符表名稱解析時,該函數將執行類似於表。
下面的範例顯示了兩個使用者定義的函數,`T_view``T_notview`並 顯示了`union`如何在 中通過通配符引用解決第一個函數。

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="user-defined-functions-usage-restrictions"></a>使用者定義的函數使用限制

適用以下限制：

* 使用者定義的函數不能傳遞到依賴於調用函數的行上下文的[scalar()](../toscalarfunction.md)調用資訊。
* 返回表格表達式的使用者定義的函數不能使用隨行上下文而變化的參數來調用。
* 在遠端群集上不能調用至少採用一個表格輸入的函數。
* 無法在遠端群集上調用標量函數。

使用者定義的函數的唯一位置可以用隨行上下文而變化的參數呼叫,即使用者定義的函數僅由標量函數組成,並且不使用`toscalar()`。

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
