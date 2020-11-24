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
ms.localizationpriority: high
ms.openlocfilehash: 582683a9261d84fa24d819b5234e58effaf90a97
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512021"
---
# <a name="the-dynamic-data-type"></a>動態資料類型

純量 `dynamic` 資料類型的特殊之處是，它可以從下列清單中取得任何其他純量資料類型的值，以及陣列和屬性包。 具體而言， `dynamic` 值可以是：

* 空。
* 任何基本純量資料類型的值： `bool` 、 `datetime` 、 `guid` 、 `int` 、 `long` 、 `real` 、 `string` 和 `timespan` 。
* 值的陣列 `dynamic` ，以零為起始的索引來保留零個或更多值。
* 將唯一值對應至值的屬性包 `string` `dynamic` 。
  屬性包有零或多個這類對應 (稱為「位置」 ) ，由唯一值編制索引 `string` 。 插槽未排序。

> [!NOTE]
> * Type 的值 `dynamic` 限制為 1mb (2 ^ 20) 。
> * 雖然 `dynamic` 型別會顯示為 json 型別，但它可以保存 json 模型不代表的值，因為它們不存在於 json (例如、、、、 `long` `real` `datetime` `timespan` 和 `guid`) 。
>   因此，在 `dynamic` 將值序列化為 json 標記法時，JSON 無法表示的值會序列化成 `string` 值。 相反地，Kusto 會將字串剖析為強型別值（如果可以將它們剖析為強型別值）。
>   這適用于 `datetime` 、 `real` 、 `long` 和 `guid` 類型。 
>   如需 JSON 物件模型的詳細資訊，請參閱 [json.org](https://json.org/)。
> * Kusto 不會嘗試保留屬性包中的名稱與值對應順序，所以您無法假設要保留的順序。 例如，兩個具有相同對應集合的屬性包都有可能會在以值表示時產生不同的結果 `string` 。

## <a name="dynamic-literals"></a>動態常值

型別的常值 `dynamic` 看起來像這樣：

`dynamic(` *值* `)`

*值* 可以是：

* `null`，在這種情況下，常值代表 null 動態值： `dynamic(null)` 。
* 另一個純量資料類型常值，在此情況下，常值代表「 `dynamic` 內部」類型的常值。 例如， `dynamic(4)` 是包含 long 純量資料類型之值4的動態值。
* 動態或其他常值的陣列： `[` *ListOfValues* `]` 。 例如， `dynamic([1, 2, "hello"])` 是三個元素的動態陣列、兩個 `long` 值和一個 `string` 值。
* 屬性包： `{` *名稱* `=` *值* ... `}` 。 例如， `dynamic({"a":1, "b":{"a":2}})` 是具有兩個位置（和）的屬性包， `a` 而 `b` 第二個插槽是另一個屬性包。

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

為了方便起見， `dynamic` 出現在查詢文字中的常值也可以包含其他 Kusto 常值，其類型為： `datetime` 、 `timespan` 、、、、 `real` `long` `guid` `bool` 和 `dynamic` 。
剖析 (字串時，無法使用此延伸模組（例如使用函式時 `parse_json` 或擷取資料) 時），但它可讓您執行下列作業：

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

若要將 `string` 遵循 JSON 編碼規則的值剖析為 `dynamic` 值，請使用 `parse_json` 函數。 例如：

* `parse_json('[43, 21, 65]')` - 數字陣列
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` -字典
* `parse_json('21')` - 包含數字的動態類型單一值
* `parse_json('"21"')` - 包含字串的動態類型單一值
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` -提供與上述範例相同的值 `o` 。

> [!NOTE]
> 與 JavaScript 不同的是，JSON 會要求在 `"` 字串和屬性包的屬性名稱前後使用雙引號 () 字元。
> 因此，使用單引號 (的) 字元來括住 JSON 編碼的字串常值，通常比較容易 `'` 。
  
下列範例示範如何定義保存資料行 (的資料表，以及資料 `dynamic` `datetime` 行) ，然後內嵌在單一記錄中。 它也會示範如何在 CSV 檔案中編碼 JSON 字串：

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

|時間戳記                   | 追蹤                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00：00：00.0000000 | {"EventValue"： "Demo"，""： "雙引號愛！"}|

## <a name="dynamic-object-accessors"></a>動態物件存取子

若要為字典加上標，請使用點標記法 (`dict.key`) ，或使用括弧標記法 (`dict["key"]`) 。
當注標是字串常數時，這兩個選項都是相等的。

> [!NOTE] 
> 若要使用運算式做為注標，請使用括弧標記法。 使用算術運算式時，括弧內的運算式必須以括弧括住。

在下列範例中 `dict` ，和 `arr` 是動態類型的資料行：

|運算式                        | 存取子運算式類型 | 意義                                                                              | 註解                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict [col]                         | 機構名稱 (資料行)      | 使用資料行的值 `col` 做為索引鍵來為字典注標              | 資料行必須是字串類型                 | 
|arr [index]                        | 實體索引 (資料行)     | 使用資料行的值 `index` 做為索引來將陣列標上標              | 資料行的類型必須是整數或布林值     | 
|arr [-index]                       | 實體索引 (資料行)     | 從陣列結尾抓取 ' index'-th 值                             | 資料行的類型必須是整數或布林值     |
|arr [ (-1) ]                         | 實體索引             | 抓取陣列中的最後一個值                                                |                                               |
|arr [toint (indexAsString) ]         | 函式呼叫            | 將資料行的值轉換 `indexAsString` 成 int，並使用它們來為數組標上 |                                               |
|dict [[' where ']]                   | 當做機構名稱使用的關鍵字 (資料行)  | 使用資料行的值做為索引鍵來對字典進行下標 `where`    | 與某些查詢語言關鍵字相同的機構名稱必須加上引號 | 
|dict. [' where '] 或 dict [' where ']   | 常數                 | 使用 `where` 字串做為索引鍵來為字典注標                              |                                               |

**效能秘訣：** 偏好盡可能使用常數注標

存取值的子物件會 `dynamic` 產生另一個 `dynamic` 值，即使子物件有不同的基礎型別也是一樣。 使用 `gettype` 函式來探索值的實際基礎類型，以及下列任何轉換函數，以將其轉換為實際類型。

## <a name="casting-dynamic-objects"></a>轉換動態物件

> 注標動態物件之後，您必須將值轉換成簡單類型。

|運算式 | 值 | 類型|
|---|---|---|
| X | parse_json ( ' [100101102] ' ) | array|
|X [0]|parse_json ( ' 100 ' ) |動態|
|toint (X [1] ) |101| int|
| Y | parse_json ( ' {"a1"：100，"a b c"： "2015-01-01"} ' ) | 字典|
|Y a1|parse_json ( ' 100 ' ) |動態|
|Y ["a b c"]| parse_json ( "2015-01-01" ) |動態|
|todate (Y ["a b c"] ) |datetime (2015-01-01) | Datetime|

轉換函數包括：

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>建立動態物件

有幾個函數可讓您建立新的 `dynamic` 物件：

* [pack ( # B1 ](../packfunction.md) 會從名稱/值組建立屬性包。
* [pack_array ( # B1 ](../packarrayfunction.md) 會從名稱/值配對建立陣列。
* [範圍 ( # B1 ](../rangefunction.md) 會以算術系列的數位來建立陣列。
* [zip ( # B1 ](../zipfunction.md) 將兩個數組中的「平行」值配對成單一陣列。
* [重複 ( # B1 ](../repeatfunction.md) 會建立具有重複值的陣列。

此外，還有數個彙總函式可建立 `dynamic` 陣列來保存匯總值：

* [buildschema ( # B1 ](../buildschema-aggfunction.md) 會傳回多個值的匯總架構 `dynamic` 。
* [make_bag ( # B1 ](../make-bag-aggfunction.md) 會傳回群組內動態值的屬性包。
* [make_bag_if ( # B1 ](../make-bag-if-aggfunction.md) 會以述詞) 傳回群組 (內動態值的屬性包。
* [make_list ( # B1 ](../makelist-aggfunction.md) 會傳回包含所有值的陣列（依序）。
* [make_list_if ( # B1 ](../makelistif-aggfunction.md) 會傳回陣列，其中包含以述詞) 順序 (的所有值。
* [make_list_with_nulls ( # B1 ](../make-list-with-nulls-aggfunction.md) 會傳回陣列，其中包含所有值（依序），包含 null 值。
* [make_set ( # B1 ](../makeset-aggfunction.md) 會傳回包含所有唯一值的陣列。
* [make_set_if ( # B1 ](../makesetif-aggfunction.md) 會傳回陣列，其中包含述詞)  (的所有唯一值。

## <a name="operators-and-functions-over-dynamic-types"></a>動態類型的運算子和函式

|運算子或函數|使用動態資料類型|
|---|---|
| *value* `in` *array*| 如果有 array 項目 == value，則為 true<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| 如果沒有 array 項目 == value，則為 true
|[`array_length(`陣 列`)`](../arraylengthfunction.md)| 如果不是陣列則為 null
|[`bag_keys(`袋`)`](../bagkeysfunction.md)| 列舉動態屬性包物件中的所有根金鑰。
|[`bag_merge(`bag1,..., bagN`)`](../bag-merge-function.md)| 將動態屬性包合併為動態屬性包，併合並所有屬性。
|[`extractjson(`path、object`)`](../extractjsonfunction.md)|使用路徑來瀏覽至物件。
|[`parse_json(`source`)`](../parsejsonfunction.md)| 將 JSON 字串變成動態物件。
|[`range(`from、to、step`)`](../rangefunction.md)| 值的陣列
|[`mv-expand` listColumn](../mvexpandoperator.md) | 在指定資料格中複寫清單中每個值的資料列。
|[`summarize buildschema(`列`)`](../buildschema-aggfunction.md) |從資料行內容推斷類型結構描述
|[`summarize make_bag(`列`)`](../make-bag-aggfunction.md) | 將資料行中的屬性包 (字典) 值合併成一個屬性包，而不需要金鑰重複。
|[`summarize make_bag_if(`column、述詞`)`](../make-bag-if-aggfunction.md) | 將資料行中的屬性包 (字典) 值合併成一個屬性包，而不使用述詞) 的金鑰重複 (。
|[`summarize make_list(`資料行 `)`](../makelist-aggfunction.md)| 將資料列群組壓平合併，並將資料行的值放在陣列中。
|[`summarize make_list_if(`column、 `)` 述詞 ](../makelistif-aggfunction.md)| 將資料列群組壓平合併，並將資料行的值放入具有述詞)  (的陣列中。
|[`summarize make_list_with_nulls(`資料行 `)`](../make-list-with-nulls-aggfunction.md)| 壓平合併資料列群組，並將資料行的值放在陣列中，包括 null 值。
|[`summarize make_set(`列`)`](../makeset-aggfunction.md) | 將資料列群組壓平合併，並將資料行的值放在陣列中，但不重複。
