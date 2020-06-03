---
title: 。 alter 內嵌對應-Azure 資料總管
description: 本文說明 Azure 資料總管中的 alter 內嵌對應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f62692c7f5a1b557038f452f5ed3c023ec9849f9
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329022"
---
# <a name="alter-ingestion-mapping"></a>.alter 擷取對應

改變與特定資料表和特定格式（完整對應取代）相關聯的現有內嵌對應。

**語法**

`.alter``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * 這個對應可以藉由內嵌命令來參考其名稱，而不是在命令中指定完整的對應。
> * _MappingKind_的有效值為： `CSV` 、 `JSON` 、 `avro` 、 `parquet` 和 `orc` 。

**範例** 
 
```kusto
.alter table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"} }'
']'

.alter table MyTable ingestion json mapping "Mapping1"
'['
'   { "column" : "rownumber", "Properties":{"Path":"$.rownumber"}},'
'   { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**範例輸出**

| 名稱     | 種類 | 對應                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
