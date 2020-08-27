---
title: 資料對應-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料對應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: ohbitton
ms.service: data-explorer
ms.topic: reference
ms.date: 05/19/2020
ms.openlocfilehash: cd498d43d98250bad0a7ce00c4a8fec7b4f3ad4f
ms.sourcegitcommit: d08b3344d7e9a6201cf01afc8455c7aea90335aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964722"
---
# <a name="data-mappings"></a>資料對應

資料對應會在內嵌期間用來將內送資料對應至 Kusto 資料表內的資料行。

Kusto 支援不同類型的對應， `row-oriented` (CSV、JSON 和 AVRO) ，以及 `column-oriented` (Parquet) 。

對應清單中的每個元素都是由三個屬性所構成：

|屬性|描述|
|----|--|
|`column`|Kusto 資料表中的目標資料行名稱|
|`datatype`|  (選擇性) 資料類型，用來建立對應的資料行（如果它尚未存在於 Kusto 資料表中）|
|`Properties`| (選擇性) 屬性包，其中包含每個對應的特定屬性，如下列各節所述。


所有對應都可以 [預先建立](create-ingestion-mapping-command.md) ，並且可以使用參數從內嵌命令參考 `ingestionMappingReference` 。

## <a name="csv-mapping"></a>CSV 對應

當原始程式檔是 CSV (或任何分隔符號分隔格式) 且其架構不符合目前的 Kusto 資料表架構時，CSV 對應會從檔案架構對應到 Kusto 資料表架構。 如果資料表不存在於 Kusto 中，則會根據此對應來建立資料表。 如果資料表中遺漏了對應中的某些欄位，就會加入這些欄位。 

CSV 對應可套用至所有分隔符號分隔格式： CSV、TSV、PSV、SCSV 和 SOHsv。

清單中的每個專案都會描述特定資料行的對應，並且可包含下列屬性：

|屬性|描述|
|----|--|
|`ordinal`|CSV 中的資料行順序編號|
|`constantValue`| (選擇性) 要用於資料行的常數值，而不是 CSV 內的一些值|

> [!NOTE]
> `Ordinal` 和 `ConstantValue` 都是互斥的。

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
> 當您在控制命令中提供上述的對應時， `.ingest` 它會序列化為 JSON 字串。

* 當上述對應 [預先建立](create-ingestion-mapping-command.md) 時，可以在控制命令中參考它 `.ingest` ：

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* 當您在控制命令中提供上述的對應時，會 `.ingest` 將其序列化為 JSON 字串：

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

**注意：** 下列不含屬性包的對應格式 `Properties` 已被取代。

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

當來源檔案是 JSON 格式時，檔案內容會對應至 Kusto 資料表。 除非針對所有對應的資料行指定有效的資料類型，否則資料表必須存在於 Kusto 資料庫中。 除非針對所有不存在的資料行指定資料類型，否則 JSON 對應中對應的資料行必須存在於 Kusto 資料表中。

清單中的每個專案都會描述特定資料行的對應，並且可包含下列屬性： 

|屬性|描述|
|----|--|
|`path`|如果開頭為 `$` ：將會成為 json 檔中資料行內容之欄位的 json 路徑 (會) 表示整份檔的 json 路徑 `$` 。 如果值的開頭不 `$` 是：會使用常數值。|
|`transform`| (應在具有 [對應轉換](#mapping-transformations)的內容上套用的選擇性) 轉換。|

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
> 當您在控制命令中提供上述的對應時， `.ingest` 它會序列化為 JSON 字串。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**注意：** 下列不含屬性包的對應格式 `Properties` 已被取代。

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

當來源檔案為 Avro 格式時，Avro 檔案內容會對應至 Kusto 資料表。 除非針對所有對應的資料行指定有效的資料類型，否則資料表必須存在於 Kusto 資料庫中。 除非針對所有不存在的資料行指定資料類型，否則 Avro 對應中對應的資料行必須存在於 Kusto 資料表中。

清單中的每個專案都會描述特定資料行的對應，並且可包含下列屬性： 

|屬性|描述|
|----|--|
|`Field`|Avro 記錄中的功能變數名稱。|
|`Path`|`field`如果有必要，則使用的替代方法允許接受 Avro 記錄欄位的內部部分。 此值代表來自記錄根目錄的 JSON 路徑。 如需詳細資訊，請參閱下列附注。 |
|`transform`| (選用的) 轉換，應套用至 [支援的轉換](#mapping-transformations)內容。|

**備註**
>[!NOTE]
> * `field` 和 `path` 不能一起使用，只允許一個。 
> * `path` 無法指向根目錄 `$` ，必須至少有一個路徑層級。

以下兩個替代專案相等：

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
> 當您在控制命令中提供上述的對應時， `.ingest` 它會序列化為 JSON 字串。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**注意：** 下列不含屬性包的對應格式 `Properties` 已被取代。

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

當來源檔案的格式為 Parquet 時，檔案內容會對應到 Kusto 資料表。 除非針對所有對應的資料行指定有效的資料類型，否則資料表必須存在於 Kusto 資料庫中。 除非針對所有不存在的資料行指定資料類型，否則 Parquet 對應中對應的資料行必須存在於 Kusto 資料表中。

清單中的每個專案都會描述特定資料行的對應，並且可包含下列屬性：

|屬性|描述|
|----|--|
|`path`|如果開頭為 `$` ：將成為 Parquet 檔中資料行內容之欄位的 json 路徑， (代表整個檔的 json 路徑 `$`) 。 如果值的開頭不 `$` 是：會使用常數值。|
|`transform`| (應套用於內容的選擇性) [對應轉換](#mapping-transformations) 。


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
> 當您在控制命令中提供上述的對應時， `.ingest` 它會序列化為 JSON 字串。

* 當上述對應 [預先建立](create-ingestion-mapping-command.md) 時，可以在控制命令中參考它 `.ingest` ：

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* 當您在控制命令中提供上述的對應時，會 `.ingest` 將其序列化為 JSON 字串：

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

當來源檔案的格式為 Orc 時，檔案內容會對應到 Kusto 資料表。 除非針對所有對應的資料行指定有效的資料類型，否則資料表必須存在於 Kusto 資料庫中。 除非針對所有不存在的資料行指定資料類型，否則 Orc 對應中對應的資料行必須存在於 Kusto 資料表中。

清單中的每個專案都會描述特定資料行的對應，並且可包含下列屬性：

|屬性|描述|
|----|--|
|`path`|如果開頭為 `$` ：將成為 Orc 檔中資料行內容之欄位的 json 路徑， (代表整個檔的 json 路徑 `$`) 。 如果值的開頭不 `$` 是：會使用常數值。|
|`transform`| (應套用於內容的選擇性) [對應轉換](#mapping-transformations) 。

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
> 當您在控制命令中提供上述的對應時， `.ingest` 它會序列化為 JSON 字串。

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

某些資料格式對應 (Parquet、JSON 和 Avro) 支援簡單且有用的內嵌階段轉換。 在內嵌階段需要更複雜的處理案例時，請使用 [更新原則](update-policy.md)，以允許使用 KQL 運算式定義輕量處理。

|路徑相依的轉換|描述|條件|
|--|--|--|
|`PropertyBagArrayToDictionary`|轉換屬性的 JSON 陣列 (例如 {events： [{"n1"： "v1"}，{"n2"： "v2"}]} ) 至字典，並將其序列化為有效的 JSON 檔 (例如 {"n1"： "v1"，"n2"： "v2"} ) 。|只有在使用時才能套用 `path`|
|`SourceLocation`|提供資料的儲存體成品名稱、輸入字串 (例如，blob 的 "BaseUri" 欄位) 。|
|`SourceLineNumber`|相對於該儲存體成品的位移，請輸入 long (從 ' 1 ' 開始，並根據新記錄) 遞增。|
|`DateTimeFromUnixSeconds`|將代表 unix 時間 (秒數的數位，從 1970-01-01) 轉換為 UTC 日期時間字串|
|`DateTimeFromUnixMilliseconds`|將代表 unix 時間 (毫秒的數位，從 1970-01-01) 轉換為 UTC 日期時間字串|
|`DateTimeFromUnixMicroseconds`|將代表 unix 時間 (微秒的數位，從 1970-01-01) 轉換為 UTC 日期時間字串|
|`DateTimeFromUnixNanoseconds`|將代表 unix 時間 (毫微秒的數位，從 1970-01-01) 轉換為 UTC 日期時間字串|
