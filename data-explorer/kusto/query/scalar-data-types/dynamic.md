---
title: 動態資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的動態數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e1cdb6e5af20b326198a7447c50c24e5f632d237
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509872"
---
# <a name="the-dynamic-data-type"></a>動態資料類型

`dynamic`標量資料類型是特殊的,因為它可以從下面的清單中獲取其他標量數據類型的任何值,以及陣列和屬性包。 具體而言,`dynamic`值可以是:

* 空。
* 任何`bool`基元標量資料類型的值: `datetime` `guid`、 `int` `long` `real` `string`、 `timespan`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、
* 值陣列`dynamic`,具有零或多個值,具有零基索引。
* 將唯一`string`值映射`dynamic`到 值的屬性包。
  屬性包具有零個或多個此類映射(稱為"插槽"),由唯一`string`值索引。 插槽未排序。

> [!NOTE]
> 類型`dynamic`值限制為 1MB (2^20)。

> [!NOTE]
> `dynamic`儘管類型顯示為類似 JSON,但它可以容納 JSON 模型不表示的值,因為它們不存在`long`JSON(`timespan``guid`例如, `real`、 `datetime`、 和 )
> 因此,在將值`dynamic`序列化為 JSON 表示形式時,JSON 無法表示`string`的值將序列化為值。 相反,Kusto 將解析字串為強類型值(如果可以這樣解析)。
> 這適用於`datetime`、`real``long`類型`guid`。 有關 JSON 物件模型的更多,請參閱[json.org](https://json.org/)。

> [!NOTE]
> Kusto 不會嘗試在屬性包中保留名稱到值映射的順序,因此您無法假定要保留的順序。 例如,具有相同映射集的兩個屬性包完全有可能在它們表示為`string`值時產生不同的結果。

## <a name="dynamic-literals"></a>動態文字

類型的`dynamic`文字如下所示:

`dynamic(`*值*`)`

*值*可以是:

* `null`在這種情況下,文字表示 null 動態`dynamic(null)`值: 。
* 另一種標量數據類型文本,在這種情況下,文本表示"內部`dynamic`"類型的文本。 例如,`dynamic(4)`是保存長標量數據類型的值 4 的動態值。
* 動態或其他文字的陣列:`[`*清單的值*`]`。 例如,`dynamic([1, 2, "hello"])`是一個由三個元素、兩`long`個 值`string`和一個值組成的動態陣列。
* 屬性套件:`{`*名稱*`=`*值*...`}`. 例如,`dynamic({"a":1, "b":{"a":2}})`是具有兩個插槽的屬性包`a`,`b`第 二個插槽是另一個屬性包。

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

為方便起見,`dynamic`查詢文本本身中出現的文本也可能包括其他 Kusto`datetime`文本(`timespan`如文本、 文本等)。在分析字串時(例如使用`parse_json`函數或引入資料時),JSON 上的此擴展不可用,但它使您能夠執行此操作:

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

要將遵循`string`JSON 編碼規則的值`dynamic`解析為 值,請使用`parse_json`函數。 例如：

* `parse_json('[43, 21, 65]')` - 數字陣列
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`- 字典
* `parse_json('21')` - 包含數字的動態類型單一值
* `parse_json('"21"')` - 包含字串的動態類型單一值
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')`- 給出與`o`上 例相同的值。

> [!NOTE]
> 與 JavaScript 不同,JSON 強制使用字串`"`和屬性 包屬性名稱周圍的雙引號 ( ) 字元。
> 因此,使用單引號()`'`字元引用 JSON 編碼的字串文本通常更容易。
  
下面的範例展示如何定義包含列`dynamic`(`datetime`以及列)的表,然後引入單個記錄。 它還展示如何在 CSV 檔中對 JSON 字串進行編碼:

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
|2015-01-01 00:00:00.0000000 | {"事件類型":"演示","事件值":"雙引號愛!"|

## <a name="dynamic-object-accessors"></a>動態物件存取器

要對字典進行下標,請使用點表示法`dict.key`( ) 或括`dict["key"]`號表示法 ( 。
當下標是字串常量時,兩個選項都是等效的。

> [!NOTE] 
> 要使用表達式作為下標,請使用括弧表示法。 使用算術表達式時,括弧內的表達式必須包在括弧中。

在下面的`dict`範例中,`arr`是動態類型的欄:

|運算是                        | 存取器運算式類型 | 意義                                                                              | 註解                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|迪克特[科爾]                         | 實體名稱(欄)     | 使用欄`col`位值作為鍵對字典進行下標              | 欄必須為型別字串                 | 
|arr[索引]                        | 實體索引(欄)    | 使用欄`index`位的值為索引對陣列進行細分              | 欄的類型必須整數或布林     | 
|arr[指數]                       | 實體索引(欄)    | 從陣列的末尾檢索「索引」第一個值                             | 欄的類型必須整數或布林     |
|arr[(-1)]                         | 實體索引             | 檢索陣列的最後一個值                                                |                                               |
|arr[toint(索引AsString)]         | 函式呼叫             | 將欄`indexAsString`值強制轉換為 int,並使用它們來對陣列進行下標 |                                               |
|迪克特[在哪裡]]                   | 用作實體名稱的關鍵字(欄) | 使用欄`where`位值作為鍵對字典進行下標    | 必須參考與某些查詢語言關鍵字相同的實體名稱 | 
|"在哪裡"或"在哪裡"*   | 持續性                 | 使用`where`字串作為鍵對字典進行子文稿                              |                                               |

**效能提示:** 盡可能使用常量子腳本

訪問值的子物件會產生另一`dynamic``dynamic`個值,即使子物件具有不同的基礎類型也是如此。 使用`gettype`函數可以發現值的實際基礎類型,以及下面列出的任何強制轉換函數將其轉換為實際類型。

## <a name="casting-dynamic-objects"></a>強制轉換動態物件

> 對動態物件進行下標後,必須將該值轉換為簡單類型。

|運算是 | 值 | 類型|
|---|---|---|
| X | parse_json('100,101,102])| array|
|X[0]|parse_json('100')|動態|
|托因特(X[1])|101| int|
| Y | parse_json("a1":100,"b c":"2015-01-01"*)| 字典|
|Y.a1|parse_json('100')|動態|
|Y="b c"*| parse_json("2015-01-01")|動態|
|日期(Y="b c"*)|日期時間 (2015-01-01)| Datetime|

強制轉換功能包括:

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>編譯動態物件

多個函數讓您能夠建立新`dynamic`物件:

* [pack()](../packfunction.md)從名稱/值對創建屬性包。
* [pack_array()](../packarrayfunction.md)從名稱/值對創建陣列。
* [range()](../rangefunction.md)建立具有數位算術序列的陣列。
* [zip()](../zipfunction.md)將兩個陣列的"並行"值配對到單個陣列中。
* [重複()](../repeatfunction.md)建立具有重複值的陣列。

此外,還有幾個聚合函數創建`dynamic`數位來儲存聚合值:

* [buildschema()](../buildschema-aggfunction.md)傳`dynamic`回多個值的聚合架構。
* [make_bag()](../make-bag-aggfunction.md)傳回組中動態值的屬性包。
* [make_bag_if())](../make-bag-if-aggfunction.md)傳回組中動態值的屬性包(帶有謂詞)。
* [make_list()](../makelist-aggfunction.md)傳回按順序保存所有值的陣列。
* [make_list_if())](../makelistif-aggfunction.md)傳回一個陣列,該陣列按順序(帶有謂詞)保存所有值。
* [make_list_with_nulls()](../make-list-with-nulls-aggfunction.md)傳回按順序保存所有值(包括空值)的陣列。
* [make_set()](../makeset-aggfunction.md)傳回包含所有唯一值的陣列。
* [make_set_if()](../makesetif-aggfunction.md)傳回包含所有唯一值(帶有謂詞)的陣列。

## <a name="operators-and-functions-over-dynamic-types"></a>動態類型的運算子和函式

|||
|---|---|
| *value* `in` *array*| 如果有 array** 項目 == value**，則為 true<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| 如果沒有 array** 項目 == value**，則為 true
|[`array_length(`陣列`)`](../arraylengthfunction.md)| 如果不是陣列則為 null
|[`bag_keys(`袋`)`](../bagkeysfunction.md)| 枚舉動態屬性包物件中的所有根鍵。
|[`extractjson(`路徑,物件`)`](../extractjsonfunction.md)|使用路徑來瀏覽至物件。
|[`parse_json(`源`)`](../parsejsonfunction.md)| 將 JSON 字串變成動態物件。
|[`range(`從,到,步驟`)`](../rangefunction.md)| 值的陣列
|[`mv-expand`清單列](../mvexpandoperator.md) | 在指定資料格中複寫清單中每個值的資料列。
|[`summarize buildschema(`欄`)`](../buildschema-aggfunction.md) |從資料行內容推斷類型結構描述
|[`summarize make_bag(`欄`)`](../make-bag-aggfunction.md) | 將列中的屬性包(字典)值合併到一個屬性袋中,而不進行密鑰複製。
|[`summarize make_bag_if(`列,謂詞`)`](../make-bag-if-aggfunction.md) | 將列中的屬性包(字典)值合併到一個屬性包中,而不進行密鑰重複(帶謂詞)。
|[`summarize make_list(`欄`)`](../makelist-aggfunction.md)| 將資料列群組壓平合併，並將資料行的值放在陣列中。
|[`summarize make_list_if(`列,謂`)`詞](../makelistif-aggfunction.md)| 拼合行組並將列的值放在數位中(帶謂詞)。
|[`summarize make_list_with_nulls(`欄`)`](../make-list-with-nulls-aggfunction.md)| 拼合行組並將列的值放在數位中,包括空值。
|[`summarize make_set(`欄`)`](../makeset-aggfunction.md) | 將資料列群組壓平合併，並將資料行的值放在陣列中，但不重複。
|[`summarize make_bag(`欄`)`](../make-bag-aggfunction.md) | 將列中的屬性包(字典)值合併到一個屬性袋中,而不進行密鑰複製。