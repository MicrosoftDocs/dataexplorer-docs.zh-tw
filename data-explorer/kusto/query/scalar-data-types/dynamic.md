---
title: 動態資料類型-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的動態資料類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/09/2020
ms.openlocfilehash: e909754a040308d752b19155e1e69a10097ab219
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280508"
---
# <a name="the-dynamic-data-type"></a>動態資料類型

純量 `dynamic` 資料類型是特殊的，它可以接受下列清單中任何其他純量資料類型的值，以及陣列和屬性包。 具體而言， `dynamic` 值可以是：

* Null.
* 任何基本純量資料類型的值： `bool` 、 `datetime` 、 `guid` 、 `int` 、、、 `long` `real` `string` 和 `timespan` 。
* 值的陣列 `dynamic` ，保留零個或多個具有以零為起始之索引的值。
* 將唯一值對應至值的屬性包 `string` `dynamic` 。
  屬性包有零或多個這類對應 (稱為「位置」 ) ，以唯一值編制索引 `string` 。 位置未排序。

> [!NOTE]
> * 類型的值 `dynamic` 限制為 1mb (2 ^ 20) 。
> * 雖然 `dynamic` 類型看起來類似 json，但它可以保留 json 模型不代表的值，因為它們不存在於 json 中 (例如、、、、 `long` `real` `datetime` `timespan` 和 `guid`) 。
>   因此，在將 `dynamic` 值序列化為 json 標記法時，JSON 無法代表的值會序列化為 `string` 值。 相反地，Kusto 會將字串剖析為強型別值（如果可以將它們剖析為）。
>   這適用于 `datetime` 、 `real` 、 `long` 和 `guid` 類型。 
>   如需 JSON 物件模型的詳細資訊，請參閱[json.org](https://json.org/)。
> * Kusto 不會嘗試保留屬性包中的名稱與值對應順序，因此您無法假設要保留的順序。 有兩個具有相同對應集的屬性包，可以在以值表示時產生不同的結果 `string` （例如）。

## <a name="dynamic-literals"></a>動態常值

類型的常值 `dynamic` 看起來像這樣：

`dynamic(` *值* `)`

*值*可以是：

* `null`，在此情況下，常值代表 null 動態值： `dynamic(null)` 。
* 另一個純量資料類型常值，在此情況下，常值代表「 `dynamic` 內部」類型的常值。 例如， `dynamic(4)` 是包含 long 純量資料類型之值4的動態值。
* 動態或其他常值的陣列： `[` *ListOfValues* `]` 。 例如， `dynamic([1, 2, "hello"])` 是三個元素的動態陣列、兩個 `long` 值和一個 `string` 值。
* 屬性包： `{` *名稱* `=` *值*... `}` 。 例如， `dynamic({"a":1, "b":{"a":2}})` 是具有兩個位置（和）的屬性包，而 `a` `b` 第二個插槽是另一個屬性包。

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

為了方便起見， `dynamic` 出現在查詢文字本身中的常值也可能包含類型為的其他 Kusto 常值： `datetime` 、 `timespan` 、 `real` 、 `long` 、、 `guid` `bool` 和 `dynamic` 。
剖析 (字串時無法使用此延伸模組，例如當使用 `parse_json` 函數或內嵌資料) 時，您可以執行下列動作：

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

若要將 `string` 遵循 JSON 編碼規則的值剖析為 `dynamic` 值，請使用 `parse_json` 函數。 例如：

* `parse_json('[43, 21, 65]')` - 數字陣列
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`-字典
* `parse_json('21')` - 包含數字的動態類型單一值
* `parse_json('"21"')` - 包含字串的動態類型單一值
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')`-提供與 `o` 上述範例中相同的值。

> [!NOTE]
> 與 JavaScript 不同的是，JSON 會要求使用雙引號 (`"` 字串和屬性包屬性名稱前後的) 字元。
> 因此，使用單引號 () 字元來括住 JSON 編碼的字串常值通常會比較容易 `'` 。
  
下列範例會示範如何定義保存資料行的資料表 `dynamic` (以及資料 `datetime` 行) ，然後內嵌至單一記錄。 它也會示範如何編碼 CSV 檔案中的 JSON 字串：

```kusto
// dynamic is just like any other type:
.create table Logs (Timestamp:datetime, Trace:dynamic)

// Everything between the "[" and "]" is parsed as a CSV line would be:
// 1. Since the JSON string includes double-quotes and commas (two characters
//    that have a special meaning in CSV), we must CSV-quote the entire second field.
// 2. CSV-quoting means adding double-quotes (") at the immediate beginning and end
//    of the field (no spaces allowed before the first double-quote or after the second
//    double-quote!)
// 3. CSV-quoting also means doubling-up every instance of a double-quotes within
//    the contents.
.ingest inline into table Logs
  [2015-01-01,"{""EventType"":""Demo"", ""EventValue"":""Double-quote love!""}"]
```

|Timestamp                   | 追蹤                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00：00：00.0000000 | {「事件2」： "Demo"，"EventValue"： "雙引號愛！"}|

## <a name="dynamic-object-accessors"></a>動態物件存取子

若要將字典加上注標，請使用點標記法 (`dict.key`) 或 () 的括弧標記法 `dict["key"]` 。
當注標是字串常數時，這兩個選項都是相等的。

> [!NOTE] 
> 若要使用運算式做為注標，請使用方括弧標記法。 使用算術運算式時，括弧內的運算式必須以括弧括住。

在下列範例中 `dict` ，和 `arr` 是動態類型的資料行：

|運算是                        | 存取子運算式類型 | 意義                                                                              | 註解                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict [col]                         | 機構名稱 (資料行)      | 使用資料行的值 `col` 做為索引鍵，做為字典的注標              | 資料行必須是字串類型                 | 
|arr [index]                        | 實體索引 (資料行)     | 使用資料行的值做為索引，將陣列設 `index` 為下標              | 資料行的類型必須是整數或布林值     | 
|arr [-index]                       | 實體索引 (資料行)     | 從陣列的結尾抓取 ' index' 第一個值                             | 資料行的類型必須是整數或布林值     |
|arr [ (-1) ]                         | 實體索引             | 抓取陣列中的最後一個值                                                |                                               |
|arr [toint (indexAsString) ]         | 函式呼叫            | 將資料行的值轉換 `indexAsString` 成 int，並使用它們來將陣列標上 |                                               |
|dict [['，where ']]                   | 用來做為機構名稱 (資料行) 的關鍵字 | 使用資料行的值做為索引鍵，將字典設 `where` 為下標    | 與某些查詢語言關鍵字相同的機構名稱必須加上引號 | 
|dict. [' where '] 或 dict [' where ']   | 持續性                 | 使用字串做為索引鍵，將字典設 `where` 為下標                              |                                               |

**效能秘訣：** 偏好盡可能使用常數注標

存取值的子物件會 `dynamic` 產生另一個 `dynamic` 值，即使子物件具有不同的基礎類型也一樣。 使用 `gettype` 函式來探索值的實際基礎類型，以及下面所列的任何轉換函數，將它轉換成實際類型。

## <a name="casting-dynamic-objects"></a>轉換動態物件

> 注標動態物件之後，您必須將值轉換成簡單類型。

|運算是 | 值 | 類型|
|---|---|---|
| X | parse_json ( ' [100101102] ' ) | array|
|X [0]|parse_json ( ' 100 ' ) |動態|
|toint (X [1] ) |101| int|
| 是 | parse_json ( ' {"a1"：100，"a b c"： "2015-01-01"} ' ) | 字典|
|Y. a1|parse_json ( ' 100 ' ) |動態|
|Y ["a b c"]| parse_json ( "2015-01-01" ) |動態|
|todate (Y ["a b c"] ) |datetime (2015-01-01) | Datetime|

Cast 函數包括：

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>建立動態物件

有數個函式可讓您建立新 `dynamic` 物件：

* [pack ( # B1](../packfunction.md)會從名稱/值配對建立屬性包。
* [pack_array ( # B1](../packarrayfunction.md)會從名稱/值配對建立陣列。
* [範圍 ( # B1](../rangefunction.md)會使用數位的算術數列來建立陣列。
* [zip ( # B1](../zipfunction.md)對兩個數組中的「平行」值組成單一陣列。
* [重複 ( # B1](../repeatfunction.md)會使用重複的值來建立陣列。

此外，還有數個彙總函式，可建立 `dynamic` 陣列來保存匯總值：

* [buildschema ( # B1](../buildschema-aggfunction.md)會傳回多個值的匯總架構 `dynamic` 。
* [make_bag ( # B1](../make-bag-aggfunction.md)會傳回群組內動態值的屬性包。
* [make_bag_if ( # B1](../make-bag-if-aggfunction.md)會使用述詞) ，傳回群組 (內動態值的屬性包。
* [make_list ( # B1](../makelist-aggfunction.md)會傳回保留所有值的陣列（依序）。
* [make_list_if ( # B1](../makelistif-aggfunction.md)會傳回陣列，其中包含以述詞) 順序 (的所有值。
* [make_list_with_nulls ( # B1](../make-list-with-nulls-aggfunction.md)會傳回陣列，其中包含包含 null 值的所有值（依序）。
* [make_set ( # B1](../makeset-aggfunction.md)會傳回包含所有唯一值的陣列。
* [make_set_if ( # B1](../makesetif-aggfunction.md)會傳回陣列，其中包含以述詞)  (的所有唯一值。

## <a name="operators-and-functions-over-dynamic-types"></a>動態類型的運算子和函式

|Operator 或函數|使用動態資料類型的用法|
|---|---|
| *value* `in` *array*| 如果有 array** 項目 == value**，則為 true<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| 如果沒有 array** 項目 == value**，則為 true
|[`array_length(`數列`)`](../arraylengthfunction.md)| 如果不是陣列則為 null
|[`bag_keys(`包`)`](../bagkeysfunction.md)| 列舉動態屬性包物件中的所有根機碼。
|[`bag_merge(`bag1,..., bagN`)`](../bag-merge-function.md)| 將動態屬性包合併為動態屬性包，併合並所有屬性。
|[`extractjson(`path、object`)`](../extractjsonfunction.md)|使用路徑來瀏覽至物件。
|[`parse_json(`來源`)`](../parsejsonfunction.md)| 將 JSON 字串變成動態物件。
|[`range(`from、to、step`)`](../rangefunction.md)| 值的陣列
|[`mv-expand`listColumn](../mvexpandoperator.md) | 在指定資料格中複寫清單中每個值的資料列。
|[`summarize buildschema(`排`)`](../buildschema-aggfunction.md) |從資料行內容推斷類型結構描述
|[`summarize make_bag(`排`)`](../make-bag-aggfunction.md) | 將資料行中的屬性包 (字典) 值合併到一個屬性包中，而不需要金鑰重複。
|[`summarize make_bag_if(`column、述詞`)`](../make-bag-if-aggfunction.md) | 將資料行中的屬性包 (字典) 值合併到一個屬性包中，而不需要使用述詞)  (金鑰重複。
|[`summarize make_list(`資料行 `)`](../makelist-aggfunction.md)| 將資料列群組壓平合併，並將資料行的值放在陣列中。
|[`summarize make_list_if(`column、 `)` 述詞](../makelistif-aggfunction.md)| 壓平合併資料列群組，並將資料行的值放在具有述詞)  (的陣列中。
|[`summarize make_list_with_nulls(`資料行 `)`](../make-list-with-nulls-aggfunction.md)| 將資料列群組壓平合併，並將資料行的值放在陣列中，包括 null 值。
|[`summarize make_set(`排`)`](../makeset-aggfunction.md) | 將資料列群組壓平合併，並將資料行的值放在陣列中，但不重複。
