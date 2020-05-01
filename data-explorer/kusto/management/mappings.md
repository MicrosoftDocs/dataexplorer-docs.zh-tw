---
title: 資料對應-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料對應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 2a3b402c04d5d1af85b2c2a042a23fbade7e2524
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617641"
---
# <a name="data-mappings"></a>資料對應

資料對應會在內嵌期間用來將傳入的資料對應到 Kusto 資料表內的資料行。

Kusto 支援不同類型的對應，包括`row-oriented` （CSV、JSON 和 AVRO）和`column-oriented` （Parquet）。

對應清單中的每個元素都是由三個屬性所構成：

|屬性|描述|
|----|--|
|`column`|Kusto 資料表中的目標資料行名稱|
|`datatype`| 選擇性要用來建立對應資料行的資料類型（如果它還不存在於 Kusto 資料表中）|
|`Properties`|選擇性屬性包，包含每個對應的特定屬性，如下一節中所述。


所有對應都可以[預先建立](create-ingestion-mapping-command.md)，並可使用`ingestionMappingReference`參數從內嵌命令加以參考。

## <a name="csv-mapping"></a>CSV 對應

當來源檔案是 CSV （或任何以分隔符號分隔的格式），而且其架構不符合目前的 Kusto 資料表架構時，CSV 對應會從檔案架構對應到 Kusto 資料表架構。 如果資料表不存在於 Kusto 中，則會根據這個對應來建立。 如果資料表中遺漏了對應中的某些欄位，將會加入它們。 

CSV 對應可以套用至所有以分隔符號分隔的格式： CSV、TSV、PSV、SCSV 和 SOHsv。

清單中的每個元素都會描述特定資料行的對應，而且可能包含下列屬性：

|屬性|描述|
|----|--|
|`ordinal`|CSV 中的資料行順序編號|
|`constantValue`|選擇性要用於資料行的常數值，而不是 CSV 內的某個值|

> [!NOTE]
> `Ordinal`和`ConstantValue`是互斥的。

### <a name="example-of-the-csv-mapping"></a>CSV 對應的範例

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
> 當上述對應當做`.ingest`控制項命令的一部分提供時，它會序列化為 JSON 字串。

* [預先建立](create-ingestion-mapping-command.md)上述對應時，可以在`.ingest` control 命令中加以參考：

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* 當上述對應當做`.ingest`控制項命令的一部分提供時，它會序列化為 JSON 字串：

```kusto
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

**注意：** 目前支援下列不含`Properties`屬性包的對應格式，但未來可能會被取代。

```kusto
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

當來源檔案是 JSON 格式時，檔案內容會對應至 Kusto 資料表。 資料表必須存在於 Kusto 資料庫中，除非已針對所有對應的資料行指定有效的 datatype。 在 JSON 對應中對應的資料行必須存在於 Kusto 資料表中，除非已針對所有非現有的資料行指定 datatype。

清單中的每個元素都會描述特定資料行的對應，而且可能包含下列屬性： 

|屬性|描述|
|----|--|
|`path`|如果開頭為`$`：將成為 json 檔中之資料行內容的欄位的 json 路徑（表示整份檔的 json 路徑為`$`）。 如果值的開頭不是`$`：會使用常數值。|
|`transform`|選擇性應該套用在具有[對應轉換](#mapping-transformations)之內容上的轉換。|

### <a name="example-of-json-mapping"></a>JSON 對應的範例

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
> 當上述對應當做`.ingest` control 命令的一部分提供時，它會序列化為 JSON 字串。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**注意：** 目前支援下列不含`Properties`屬性包的對應格式，但未來可能會被取代。

```kusto
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
    
## <a name="avro-mapping"></a>Avro 對應

當來源檔案是 Avro 格式時，Avro 檔案內容會對應至 Kusto 資料表。 資料表必須存在於 Kusto 資料庫中，除非已針對所有對應的資料行指定有效的 datatype。 在 Avro 對應中對應的資料行必須存在於 Kusto 資料表中，除非已針對所有非現有的資料行指定 datatype。

清單中的每個元素都會描述特定資料行的對應，而且可能包含下列屬性： 

|屬性|描述|
|----|--|
|`Field`|Avro 記錄中的功能變數名稱。|
|`Path`|使用`field`的替代方法，可讓您視需要取得 Avro 記錄欄位的內部部分。 值表示來自記錄根目錄的 JSON 路徑。 如需詳細資訊，請參閱下面的附注。 |
|`transform`|選擇性應該使用[支援的轉換](#mapping-transformations)在內容上套用的轉換。|

**注意事項**
>[!NOTE]
> * `field`和`path`無法一起使用，只允許一個。 
> * `path`無法指向根`$` ，它必須至少有一個路徑層級。

以下兩個替代方案相同：

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

### <a name="example-of-the-avro-mapping"></a>AVRO 對應的範例

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
> 當上述對應當做`.ingest` control 命令的一部分提供時，它會序列化為 JSON 字串。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**注意：** 目前支援下列不含`Properties`屬性包的對應格式，但未來可能會被取代。

```kusto
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

## <a name="parquet-mapping"></a>Parquet 對應

當來源檔案為 Parquet 格式時，檔案內容會對應到 Kusto 資料表。 資料表必須存在於 Kusto 資料庫中，除非已針對所有對應的資料行指定有效的 datatype。 在 Parquet 對應中對應的資料行必須存在於 Kusto 資料表中，除非已針對所有非現有的資料行指定 datatype。

清單中的每個元素都會描述特定資料行的對應，而且可能包含下列屬性：

|屬性|描述|
|----|--|
|`path`|如果開頭為`$`：將成為 Parquet 檔中之資料行內容的欄位的 json 路徑（表示整份檔的 json 路徑為`$`）。 如果值的開頭不是`$`：會使用常數值。|
|`transform`|選擇性應套用至內容的[對應轉換](#mapping-transformations)。


### <a name="example-of-the-parquet-mapping"></a>Parquet 對應的範例

```json
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
> 當上述對應當做`.ingest`控制項命令的一部分提供時，它會序列化為 JSON 字串。

* [預先建立](create-ingestion-mapping-command.md)上述對應時，可以在`.ingest` control 命令中加以參考：

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* 當上述對應當做`.ingest`控制項命令的一部分提供時，它會序列化為 JSON 字串：

```kusto
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

當來源檔案為 Orc 格式時，檔案內容會對應到 Kusto 資料表。 資料表必須存在於 Kusto 資料庫中，除非已針對所有對應的資料行指定有效的 datatype。 在 Orc 對應中對應的資料行必須存在於 Kusto 資料表中，除非已針對所有非現有的資料行指定 datatype。

清單中的每個元素都會描述特定資料行的對應，而且可能包含下列屬性：

|屬性|描述|
|----|--|
|`path`|如果開頭為`$`：將成為 Orc 檔中之資料行內容的欄位的 json 路徑（表示整份檔的 json 路徑為`$`）。 如果值的開頭不是`$`：會使用常數值。|
|`transform`|選擇性應套用至內容的[對應轉換](#mapping-transformations)。

### <a name="example-of-orc-mapping"></a>Orc 對應的範例

```json
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
> 當上述對應當做`.ingest`控制項命令的一部分提供時，它會序列化為 JSON 字串。

```kusto
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

## <a name="mapping-transformations"></a>對應轉換

部分資料格式對應（Parquet、JSON 和 Avro）支援簡單且有用的內嵌時間轉換。 案例在內嵌時需要更複雜的處理，請使用[更新原則](update-policy.md)，這可讓您使用 KQL 運算式來定義輕量處理。

|路徑相依的轉換|描述|條件|
|--|--|--|
|`PropertyBagArrayToDictionary`|將屬性的 JSON 陣列（例如 {events： [{"n1"： "v1"}，{"n2"： "v2"}]}）轉換成字典，並將其序列化為有效的 JSON 檔（例如 {"n1"： "v1"，"n2"： "v2"}）。|只有在使用時`path`才可以套用|
|`GetPathElement(index)`|根據指定的索引，從指定的路徑中解壓縮專案（例如，Path： $. b. .c，GetPathElement （0） = = "c"，GetPathElement （-1） = = "b"，類型 string|只有在使用時`path`才可以套用|
|`SourceLocation`|提供資料的儲存體成品名稱，類型字串（例如，blob 的 "BaseUri" 欄位）。|
|`SourceLineNumber`|相對於該儲存體成品的位移，輸入 long （從 ' 1 ' 開始，並根據新的記錄遞增）。|
|`DateTimeFromUnixSeconds`|將代表 unix 時間（從1970-01-01 起的秒數）的數位轉換為 UTC 日期時間字串|
|`DateTimeFromUnixMilliseconds`|將代表 unix 時間（從1970-01-01 起的毫秒）的數位轉換為 UTC 日期時間字串|
|`DateTimeFromUnixMicroseconds`|將代表 unix 時間（從1970-01-01 起微秒）的數位轉換為 UTC 日期時間字串|
|`DateTimeFromUnixNanoseconds`|將代表 unix 時間（從1970-01-01 開始的毫微秒）的數位轉換為 UTC 日期時間字串|
