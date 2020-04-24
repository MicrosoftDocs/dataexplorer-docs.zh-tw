---
title: .alter 引入映射 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 .alter 引入映射。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5343d55fadafce552c5d837e5eb50763ccf45a4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522401"
---
# <a name="alter-ingestion-mapping"></a>.alter 擷取對應

更改與特定表和特定格式關聯的現有引入映射(替換完全映射)。

**語法**

`.alter``table`*表名*`ingestion`*映射名稱*`mapping`*MappingName**映射映射格式 AsJson*

> [!NOTE]
> * 可以通過引入命令(而不是將完整映射指定為命令的一部分)來引用此映射的名稱。
> * _對應金德_`CSV`的有效值為: 、 `JSON` `avro` `parquet`、`orc`、 與 。

**範例** 
 
```
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
| 映射1 | CSV  | {"名稱":"行號","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null,{"名稱":"行吉德","數據類型":"字串","CsvDataType":null,"Ordinal":1,"ConstValue":null] |