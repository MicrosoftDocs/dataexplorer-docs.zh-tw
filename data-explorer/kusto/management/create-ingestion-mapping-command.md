---
title: 。建立內嵌對應-Azure 資料總管 |Microsoft Docs
description: 本文將說明如何在 Azure 資料總管中建立內嵌對應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 84ab277f5b0d4d1b2e09d31fb7c1254786affe6d
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617726"
---
# <a name="create-ingestion-mapping"></a>.create 擷取對應

建立與特定資料表和特定格式相關聯的內嵌對應。

**語法**

`.create``table` *TableName* TableName `ingestion` *MappingKind* MappingKind `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * 建立之後，就可以使用內嵌命令中的名稱來參考對應，而不是在命令過程中指定完整的對應。
> * _MappingKind_的有效值為：、 `CSV` `JSON`、 `avro`、 `parquet`和。`orc`
> * 如果資料表已有相同名稱的對應：
>    * `.create`將會失敗
>    * `.create-or-alter`會改變現有的對應
 
**範例** 
 
```kusto
.create table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"}}'
']'

.create-or-alter table MyTable ingestion json mapping "Mapping1"
'['
'    { "column" : "rownumber", "datatype" : "int", "Properties":{"Path":"$.rownumber"}},'
'    { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**範例輸出**

| 名稱     | 種類 | 對應                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | [{"Name"： "rownumber"，"DataType"： "int"，"CsvDataType"： null，"序數"：0，"ConstValue"： null}，{"Name"： "rowguid"，"DataType"： "string"，"CsvDataType"： null，"序數"：1，"ConstValue"： null}] |

## <a name="next-steps"></a>後續步驟
如需有關內嵌對應的詳細資訊，請參閱[資料](mappings.md)對應。
