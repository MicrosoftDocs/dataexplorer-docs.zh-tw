---
title: 動態資料類型 - Azure 資料總管 | Microsoft Docs
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
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512021"
---
# <a name="the-dynamic-data-type"></a>動態資料類型

`dynamic` 純量資料類型很特殊，其可接受下列清單中其他純量資料類型的任何值，以及陣列和屬性包。 具體而言，`dynamic` 值可以是：

* Null。
* 任何基本純量資料類型的值：`bool`、`datetime`、`guid`、`int`、`long`、`real`、`string` 和 `timespan`。
* `dynamic` 值的陣列，保有零個或多個具有以零為起始索引的值。
* 可將唯一 `string` 值對應至 `dynamic` 值的屬性包。
  屬性包有零個或多個這類對應 (稱為「位置」)，依唯一的 `string` 值編制索引。 位置並未排序。

> [!NOTE]
> * `dynamic` 類型的值受限於 1MB (2^20)。
> * 雖然 `dynamic` 類型看起來類似 JSON，但其可保有 JSON 模型不表示的值，因為這些值不存在於 JSON 中 (例如 `long`、`real`、`datetime`、`timespan` 和 `guid`)。
>   因此，在將 `dynamic` 值序列化為 JSON 表示法時，JSON 無法表示的值會序列化為 `string` 值。 相反地，Kusto 會將字串剖析為強型別值 (如果可以剖析的話)。
>   這適用於 `datetime`、`real`、`long` 和 `guid` 類型。 
>   如需 JSON 物件模型的詳細資訊，請參閱 [json.org](https://json.org/)。
> * Kusto 不會嘗試保留屬性包中名稱與值對應的順序，因此您無法採用要保留的順序。 例如，兩個具有相同對應集的屬性包，完全可能在以 `string` 值表示時產生不同的結果。

## <a name="dynamic-literals"></a>動態常值

`dynamic` 類型的常值如下所示：

`dynamic(` *Value* `)`

*Value* 可以是：

* `null`，在此情況下，常值代表 null 動態值：`dynamic(null)`。
* 另一個純量資料類型常值，在此情況下，常值代表「內部」類型的 `dynamic` 常值。 例如，`dynamic(4)` 是一個動態值，其中保有 long 純量資料類型的值 4。
* 動態或其他常值的陣列：`[` *ListOfValues* `]`。 例如，`dynamic([1, 2, "hello"])` 是一個動態陣列，其中包含三個元素、兩個 `long` 值和一個 `string` 值。
* 屬性包：`{` *Name* `=` *Value* ... `}`。 例如，`dynamic({"a":1, "b":{"a":2}})` 是一個屬性包，其中具有兩個位置 (`a` 和 `b`)，而第二個位置是另一個屬性包。

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

為了方便起見，出現在查詢文字本身的 `dynamic` 常值也可能包含以下類型的其他 Kusto 常值：`datetime`、`timespan`、`real`、`long`、`guid`、`bool` 和 `dynamic`。
剖析字串時 (例如使用 `parse_json` 函式或內嵌資料時)，此擴充功能無法用於 JSON，但可讓您執行下列作業：

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

若要將遵循 JSON 編碼規則的 `string` 值剖析為 `dynamic` 值，請使用 `parse_json` 函式。 例如：

* `parse_json('[43, 21, 65]')` - 數字陣列
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` - 字典
* `parse_json('21')` - 包含數字的動態類型單一值
* `parse_json('"21"')` - 包含字串的動態類型單一值
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` - 提供與上述範例中 `o` 相同的值。

> [!NOTE]
> 與 JavaScript 不同的是，JSON 會要求在字串和屬性包屬性名稱前後使用雙引號 (`"`) 字元。
> 因此，使用單引號 (`'`) 字元引述 JSON 編碼的字串常值通常比較容易。
  
下列範例示範如何定義保有一個 `dynamic` 資料行 (以及一個 `datetime` 資料行) 的資料表，然後將單一記錄內嵌到其中。 此外，也會示範如何編碼 CSV 檔案中的 JSON 字串：

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
|2015-01-01 00:00:00.0000000 | {"EventType":"Demo","EventValue":"Double-quote love!"}|

## <a name="dynamic-object-accessors"></a>動態物件存取子

若要替字典加上註標，請使用點標記法 (`dict.key`) 或方括號標記法 (`dict["key"]`)。
當註標是字串常數時，這兩個選項相等。

> [!NOTE] 
> 若要使用運算式作為註標，請使用方括號標記法。 使用算術運算式時，方括號內的運算式必須以括弧括住。

在下列範例中，`dict` 和 `arr` 是動態類型的資料行：

|運算式                        | 存取子運算式類型 | 意義                                                                              | 註解                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | 實體名稱 (資料行)     | 使用資料行 `col` 的值作為索引鍵以替字典加上註標              | 資料行的類型必須是字串                 | 
|arr[index]                        | 實體索引 (資料行)    | 使用資料行 `index` 的值作為索引以替陣列加上註標              | 資料行的類型必須是整數或布林值     | 
|arr[-index]                       | 實體索引 (資料行)    | 從陣列的結尾擷取第 'index' 個值                             | 資料行的類型必須是整數或布林值     |
|arr[(-1)]                         | 實體索引             | 擷取陣列中的最後一個值                                                |                                               |
|arr[toint(indexAsString)]         | 函式呼叫            | 將資料行 `indexAsString` 的值轉換成 int，並將其用於替陣列加上註標 |                                               |
|dict[['where']]                   | 當作實體名稱 (資料行) 使用的關鍵字 | 使用資料行 `where` 的值作為索引鍵以字典加上註標    | 與某些查詢語言關鍵字相同的實體名稱必須加上引號 | 
|dict.['where'] or dict['where']   | 常數                 | 使用 `where` 字串作為索引鍵以替字典加上註標                              |                                               |

**效能提示：** 偏好盡可能使用常數註標

存取 `dynamic` 值的子物件會產生另一個 `dynamic` 值，即使子物件具有不同的基礎類型亦然。 使用 `gettype` 函式來探索值的實際基礎類型，以及使用下面所列的任何轉換函數將其轉換為實際類型。

## <a name="casting-dynamic-objects"></a>轉換動態物件

> 替動態物件加上註標之後，您必須將值轉換成簡單類型。

|運算式 | 值 | 類型|
|---|---|---|
| X | parse_json('[100,101,102]')| array|
|X[0]|parse_json('100')|動態|
|toint(X[1])|101| int|
| Y | parse_json('{"a1":100, "a b c":"2015-01-01"}')| 字典|
|Y.a1|parse_json('100')|動態|
|Y["a b c"]| parse_json("2015-01-01")|動態|
|todate(Y["a b c"])|datetime(2015-01-01)| Datetime|

Cast 函數包括：

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>建立動態物件

有數個函數可讓您建立新的 `dynamic` 物件：

* [pack()](../packfunction.md) 會從名稱/值配對建立屬性包。
* [pack_array()](../packarrayfunction.md) 會從名稱/值配對建立陣列。
* [range()](../rangefunction.md) 會使用算術數列來建立陣列。
* [zip()](../zipfunction.md) 會將兩個數組中的「平行」值配對成單一陣列。
* [repeat()](../repeatfunction.md) 會以重複的值來建立陣列。

此外，還有數個彙總函式可建立 `dynamic` 陣列以保存彙總值：

* [buildschema()](../buildschema-aggfunction.md) 會傳回多個 `dynamic` 值的彙總結構描述。
* [make_bag()](../make-bag-aggfunction.md) 會傳回群組內動態值的屬性包。
* [make_bag_if()](../make-bag-if-aggfunction.md) 會傳回群組內動態值的屬性包 (使用述詞)。
* [make_list()](../makelist-aggfunction.md) 會傳回保有所有值 (依序排列) 的陣列。
* [make_list_if()](../makelistif-aggfunction.md) 會傳回保有所有值 (依序排列) 的陣列 (使用述詞)。
* [make_list_with_nulls()](../make-list-with-nulls-aggfunction.md) 會傳回保有所有值 (依序排列) 的陣列，其中包含 null 值。
* [make_set()](../makeset-aggfunction.md) 會傳回保有所有唯一值的陣列。
* [make_set_if()](../makesetif-aggfunction.md) 會傳回保有所有唯一值的陣列 (使用述詞)。

## <a name="operators-and-functions-over-dynamic-types"></a>動態類型的運算子和函式

|運算子或函式|搭配動態資料類型的使用方式|
|---|---|
| *value* `in` *array*| 如果有 array 項目 == value，則為 true<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| 如果沒有 array 項目 == value，則為 true
|[`array_length(`array`)`](../arraylengthfunction.md)| 如果不是陣列則為 null
|[`bag_keys(`bag`)`](../bagkeysfunction.md)| 列舉動態屬性包物件中的所有根索引鍵。
|[`bag_merge(`bag1,...,bagN`)`](../bag-merge-function.md)| 將多個動態屬性包合併為一個已合併所有屬性的動態屬性包。
|[`extractjson(`path,object`)`](../extractjsonfunction.md)|使用路徑來瀏覽至物件。
|[`parse_json(`source`)`](../parsejsonfunction.md)| 將 JSON 字串變成動態物件。
|[`range(`from,to,step`)`](../rangefunction.md)| 值的陣列
|[`mv-expand` listColumn](../mvexpandoperator.md) | 在指定資料格中複寫清單中每個值的資料列。
|[`summarize buildschema(`直條圖`)`](../buildschema-aggfunction.md) |從資料行內容推斷類型結構描述
|[`summarize make_bag(`直條圖`)`](../make-bag-aggfunction.md) | 將資料行中的屬性包 (字典) 值合併為一個屬性包，而不需要複製索引鍵。
|[`summarize make_bag_if(`column,predicate`)`](../make-bag-if-aggfunction.md) | 將資料行中的屬性包（字典）值合併為一個屬性包中，而不需要複製索引鍵 (使用述詞)。
|[`summarize make_list(`column`)` ](../makelist-aggfunction.md)| 將資料列群組壓平合併，並將資料行的值放在陣列中。
|[`summarize make_list_if(`column,predicate`)` ](../makelistif-aggfunction.md)| 將資料列群組壓平合併，並將資料行的值放在陣列中 (使用述詞)。
|[`summarize make_list_with_nulls(`column`)` ](../make-list-with-nulls-aggfunction.md)| 將資料列群組壓平合併，並將資料行的值放在陣列中 (包括 null 值)。
|[`summarize make_set(`直條圖`)`](../makeset-aggfunction.md) | 將資料列群組壓平合併，並將資料行的值放在陣列中，但不重複。
