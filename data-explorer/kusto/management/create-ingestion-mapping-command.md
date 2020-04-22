---
title: .創建引入映射 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 .創建 Azure 數據資源管理器中的引入映射。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 10e656b074516ad8b0018e627d9904251aebbf10
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744486"
---
# <a name="create-ingestion-mapping"></a>.create 擷取對應

創建與特定表和特定格式關聯的引入映射。

**語法**

`.create``table`*表名*`ingestion`*映射名稱*`mapping`*MappingName**映射映射格式 AsJson*

> [!NOTE]
> * 創建後,映射可以在引入命令中由其名稱引用,而不是指定完整映射作為命令的一部分。
> * _對應金德_`CSV`的有效值為: 、 `JSON` `avro` `parquet`、 、 和`orc`
> * 如果表已存在同名映射:
>    * `.create`失敗
>    * `.create-or-alter`將變更現有對應
 
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
| 映射1 | CSV  | {"名稱":"行號","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null,{"名稱":"行吉德","數據類型":"字串","CsvDataType":null,"Ordinal":1,"ConstValue":null] |
