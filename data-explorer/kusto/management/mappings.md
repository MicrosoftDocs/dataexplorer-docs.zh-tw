---
title: 資料映射 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的數據映射。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0d94815eedfd551a09a979c57c68baf125abec40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520769"
---
# <a name="data-mappings"></a>資料對應

數據映射在引入過程中用於將傳入資料映射到 Kusto 表中的列。

Kusto 支援不同類型的映射,`row-oriented`包括 (CSV、JSON 和`column-oriented`AVRO)和 (Parquet)。

映射清單中的每個元素由三個屬性建構:

|屬性|描述|
|----|--|
|`column`|庫斯特表中的目標列名稱|
|`datatype`| ( 選擇性的 )資料類型,用於建立映射欄(如果 Kusto 表中不存在)|
|`Properties`|( 選擇性的 )屬性包包含特定於每個映射的屬性,如下所述。


所有映射都可以[預先創建](create-ingestion-mapping-command.md),並且`ingestionMappingReference`可以使用 參數從引入命令引用。

## <a name="csv-mapping"></a>CSV 對應

當源檔是 CSV(或任何分散的解說器分離格式)並且其架構與當前的 Kusto 表架構不匹配時,CSV 映射從檔架構對應到 Kusto 表架構。 如果庫斯特圖中不存在該表,則將根據此映射創建該表。 如果表中缺少映射中的某些欄位,則將添加這些欄位。 

CSV 對應可應用於所有分隔符分隔格式:CSV、TSV、PSV、SCSV 和 SOHsv。

清單中的每個元素都描述了特定欄射,並可能包含以下屬性:

|屬性|描述|
|----|--|
|`ordinal`|CSV 中的列訂單號|
|`constantValue`|( 選擇性的 )為列而不是 CSV 內某些值的常數值|

> [!NOTE]
> `Ordinal`是`ConstantValue`相互排斥的。

### <a name="example-of-the-csv-mapping"></a>CSV 對應範例

``` json
[
  { "column" : "rownumber", "Properties":{"Ordinal":"0"}},
  { "column" : "rowguid",   "Properties":{"Ordinal":"1"}},
  { "column" : "xdouble",   "Properties":{"Ordinal":"2"}},
  { "column" : "xbool",     "Properties":{"Ordinal":"3"}},
  { "column" : "xint32",    "Properties":{"Ordinal":"4"}},
  { "column" : "xint64",    "Properties":{"Ordinal":"5"}},
  { "column" : "xdate",     "Properties":{"Ordinal":"6"}},
  { "column" : "xtext",     "Properties":{"Ordinal":"7"}},
  { "column" : "const_val", "Properties":{"ConstValue":"Sample: constant value"}}
]
```

> [!NOTE]
> 當上述映射作為控制命令的`.ingest`一部分提供時,它將序列化為 JSON 字串。

* 預先[建立](create-ingestion-mapping-command.md)此映射後`.ingest`, 可以在控制命令中引用它:
```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* 當上述映射作為控制命令的`.ingest`一部分提供時,它將序列化為 JSON 字串:

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Properties\":{\"Ordinal\":0}},"
            "{\"column\":\"rowguid\",  \"Properties\":{\"Ordinal\":1}}"
        "]" 
    )
```

**註:** 當前支援以下映射格式(沒有`Properties`屬性包,但將來可能會棄用)。

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Ordinal\": 0},"
            "{\"column\":\"rowguid\",  \"Ordinal\": 1}"
        "]" 
    )
```

## <a name="json-mapping"></a>JSON 對應

當源文件採用 JSON 格式時,檔內容將映射到 Kusto 表。 除非為所有映射的列指定有效的資料類型,否則該表必須存在於 Kusto 資料庫中。 在 JSON 映射中對應的列必須存在於 Kusto 表中,除非為所有不存在的列指定了數據類型。

清單中的每個元素都描述了特定欄射,並可能包含以下屬性: 

|屬性|描述|
|----|--|
|`path`|如果以`$`: JSON 路徑開頭,該路徑將成為 JSON 文檔中列的內容(表示整個`$`文檔的 JSON 路徑是 )。 如果值不以`$`開頭,則使用常量值。|
|`transform`|( 選擇性的 )應套用到[映射轉換的內容的轉換](#mapping-transformations)。|

### <a name="example-of-json-mapping"></a>JSON 對應範例

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "rowguid",     "Properties":{"Path":"$.rowguid"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint32",      "Properties":{"Path":"$.xint32"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```

> [!NOTE]
> 當上述映射作為`.ingest`控制命令的一部分提供時,它將序列化為 JSON 字串。

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**註:** 當前支援以下映射格式(沒有`Properties`屬性包,但將來可能會棄用)。

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "json", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"path\":\"$.rownumber\"},"
        "{\"column\":\"rowguid\",  \"path\":\"$.rowguid\"}"
      "]"
  )
```
    
## <a name="avro-mapping"></a>阿夫羅映射

當源文件採用 Avro 格式時,Avro 檔內容將映射到 Kusto 表。 除非為所有映射的列指定有效的資料類型,否則該表必須存在於 Kusto 資料庫中。 在 Avro 映射中對應的列必須存在於 Kusto 表中,除非為所有不存在的列指定數據類型。

清單中的每個元素都描述了特定欄射,並可能包含以下屬性: 

|屬性|描述|
|----|--|
|`Field`|Avro 記錄中的欄位的名稱。|
|`Path`|替代使用`field`,允許採取Avro記錄場的內部部分,如有必要。 該值表示來自記錄根目錄的 JSON 路徑。 有關詳細資訊,請參閱下面的註釋。 |
|`transform`|( 選擇性的 )應套用於具有[支援的轉換的內容的轉換](#mapping-transformations)。|

**注意事項**
>[!NOTE]
> * `field`不能`path`一起使用,只允許使用。 
> * `path`不能僅指向根`$`,它必須至少有一個級別的路徑。

以下兩種備選方案相等:

``` json
[
  {"column": "rownumber", "Properties":{"Path":"$.RowNumber"}}
]
```

``` json
[
  {"column": "rownumber", "Properties":{"Field":"RowNumber"}}
]
```

### <a name="example-of-the-avro-mapping"></a>AVRO 對應範例

``` json
[
  {"column": "rownumber", "Properties":{"Field":"rownumber"}},
  {"column": "rowguid",   "Properties":{"Field":"rowguid"}},
  {"column": "xdouble",   "Properties":{"Field":"xdouble"}},
  {"column": "xboolean",  "Properties":{"Field":"xboolean"}},
  {"column": "xint32",    "Properties":{"Field":"xint32"}},
  {"column": "xint64",    "Properties":{"Field":"xint64"}},
  {"column": "xdate",     "Properties":{"Field":"xdate"}},
  {"column": "xtext",     "Properties":{"Field":"xtext"}}
]
``` 

> [!NOTE]
> 當上述映射作為`.ingest`控制命令的一部分提供時,它將序列化為 JSON 字串。

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**註:** 當前支援以下映射格式(沒有`Properties`屬性包,但將來可能會棄用)。

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "avro", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"field\":\"rownumber\"},"
        "{\"column\":\"rowguid\",  \"field\":\"rowguid\"}"
      "]"
  )
```

## <a name="parquet-mapping"></a>鑲木地板映射

當來源檔案採用 Parquet 格式時,檔內容將映射到 Kusto 表。 除非為所有映射的列指定有效的資料類型,否則該表必須存在於 Kusto 資料庫中。 除非為所有不存在的列指定數據類型,否則在 Parquet 映射中映射的列必須存在於 Kusto 表中。

清單中的每個元素都描述了特定欄射,並可能包含以下屬性:

|屬性|描述|
|----|--|
|`path`|如果以`$`: JSON 路徑開頭,則該路徑將成為 Parquet 文檔中列的內容(表示整個`$`文檔的 JSON 路徑為 )。 如果值不以`$`開頭,則使用常量值。|
|`transform`|( 選擇性的 )[映射](#mapping-transformations)應應用於內容的轉換。


### <a name="example-of-the-parquet-mapping"></a>鑲木地板映射範例

``` json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> 當上述映射作為控制命令的`.ingest`一部分提供時,它將序列化為 JSON 字串。

* 預先[建立](create-ingestion-mapping-command.md)此映射後`.ingest`, 可以在控制命令中引用它:

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* 當上述映射作為控制命令的`.ingest`一部分提供時,它將序列化為 JSON 字串:

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "parquet", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="orc-mapping"></a>Orc 對應

當來源檔案採用 Orc 格式時,檔內容將映射到 Kusto 表。 除非為所有映射的列指定有效的資料類型,否則該表必須存在於 Kusto 資料庫中。 在 Orc 映射中對應的列必須存在於 Kusto 表中,除非為所有不存在的列指定了數據類型。

清單中的每個元素都描述了特定欄射,並可能包含以下屬性:

|屬性|描述|
|----|--|
|`path`|如果以`$`: JSON 路徑開頭,則該路徑將成為 Orc 文檔中列的內容(表示整個`$`文檔的 JSON 路徑為 )。 如果值不以`$`開頭,則使用常量值。|
|`transform`|( 選擇性的 )[映射](#mapping-transformations)應應用於內容的轉換。

### <a name="example-of-orc-mapping"></a>Orc 對應範例

``` json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> 當上述映射作為控制命令的`.ingest`一部分提供時,它將序列化為 JSON 字串。

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "orc", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="mapping-transformations"></a>對應

某些資料格式對應(Parquet、JSON和 Avro)支援簡單而有用的引入時間轉換。 如果方案需要在引入時間進行更複雜的處理,請使用 Update[策略](update-policy.md),該策略允許使用 KQL 運算式定義輕量級處理。

|與路徑相依轉換|描述|條件|
|--|--|--|
|`PropertyBagArrayToDictionary`|將 JSON 屬性陣列(例如[事件:]{{"n1":"v1"},"n2":"v2"*)轉換為字典並將其序列化為有效的 JSON 文檔(例如,{"n1":"v1","n2":"v2"})。|只能使用時`path`套用|
|`GetPathElement(index)`|根據給定的索引從給定的路徑中提取元素(例如,路徑:$.a.b.c,GetPathElement(0) = "c",GetPathElement(-1) = "b",類型字串|只能使用時`path`套用|
|`SourceLocation`|提供資料的儲存專案的名稱,鍵入字串(例如,blob 的"BaseUri"欄位)。|
|`SourceLineNumber`|相對於該存儲工件的偏移量,鍵入長(從"1"開始,然後根據新記錄遞增)。|
|`DateTimeFromUnixSeconds`|將表示 unix 時間(自 1970-01-01 年以來的秒數)轉換為 UTC 日期時間字串|
|`DateTimeFromUnixMilliseconds`|將表示 unix 時間(自 1970-01-01 年以來毫秒)的數位轉換為 UTC 日期時間字串|
|`DateTimeFromUnixMicroseconds`|將表示 unix 時間的數位(自 1970-01-01 年以來的微秒)轉換為 UTC 日期時間字串|
|`DateTimeFromUnixNanoseconds`|將表示 unix 時間(自 1970-01-01 年以來的奈元)的數位轉換為 UTC 日期時間字串|