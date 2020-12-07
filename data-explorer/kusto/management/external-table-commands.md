---
title: Kusto 外部資料表一般控制命令-Azure 資料總管
description: 本文說明一般外部資料表控制項命令
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2020
ms.openlocfilehash: 7646f86c9a521ab45cf83d7704084f3a5df6b256
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321381"
---
# <a name="external-table-general-control-commands"></a>外部資料表一般控制項命令

如需外部資料表的總覽，請參閱 [外部資料表](../query/schema-entities/externaltables.md) 。 下列命令與任何類型)  (的 _任何_ 外部資料表相關。

## <a name="show-external-tables"></a>。顯示外部資料表

* 傳回資料庫 (中的所有外部資料表，或) 的特定外部資料表。
* 需要 [資料庫監視器許可權](../management/access-control/role-based-authorization.md)。

**語法：** 

`.show` `external` `tables`

`.show` `external` `table` *TableName*

**輸出**

| 輸出參數 | 類型   | 描述                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | 字串 | 外部資料表的名稱                                             |
| TableType        | 字串 | 外部資料表的類型                                              |
| 資料夾           | 字串 | 資料表的資料夾                                                     |
| DocString        | 字串 | 記錄資料表的字串                                       |
| 屬性       | 字串 | 資料表的 JSON 序列化屬性 (資料表類型的特定)  |


**範例：**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | 資料夾         | DocString | 屬性 |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | ExternalTables | Docs      | {}         |


## <a name="show-external-table-schema"></a>。顯示外部資料表架構

* 以 JSON 或 CSL 的形式傳回外部資料表的架構。 
* 需要 [資料庫監視器許可權](../management/access-control/role-based-authorization.md)。

**語法：** 

`.show``external` `table` *TableName* `schema` `as` (`json`  |  `csl`) 

`.show` `external` `table` *TableName* `cslschema`

**輸出**

| 輸出參數 | 類型   | 描述                        |
|------------------|--------|------------------------------------|
| TableName        | 字串 | 外部資料表的名稱            |
| 結構描述           | 字串 | JSON 格式的資料表架構 |
| DatabaseName     | 字串 | 資料表的資料庫名稱             |
| 資料夾           | 字串 | 資料表的資料夾                    |
| DocString        | 字串 | 記錄資料表的字串      |

**範例：**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**輸出：**

*Json：*

| TableName | 結構描述    | DatabaseName | 資料夾         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name"： "ExternalBlob"，<br>"Folder"： "ExternalTables"，<br>"DocString"： "檔"，<br>"OrderedColumns"： [{"Name"： "x"，"Type"： "system.string"，"CslType"： "long"，"DocString"： ""}，{"Name"： "s"，"Type"： "system.string"，"CslType"： "String"，"DocString"： ""}]} | DB           | ExternalTables | Docs      |


*Csl：*

| TableName | 結構描述          | DatabaseName | 資料夾         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long,s:string | DB           | ExternalTables | Docs      |

## <a name="drop-external-table"></a>. drop external table

* 卸載外部資料表 
* 無法在此作業之後還原外部資料表定義
* 需要 [資料庫管理員許可權](../management/access-control/role-based-authorization.md)。

**語法：**  

`.drop``external` `table` *TableName* [ `ifexists` ]

**輸出**

傳回已卸載之資料表的屬性。 如需詳細資訊，請參閱 [`.show external tables`](#show-external-tables) \(英文\)。

**範例：**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | 資料夾         | DocString | 結構描述       | 屬性 |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | ExternalTables | Docs      | [{"Name"： "x"，"CslType"： "long"}，<br> {"Name"： "s"，"CslType"： "string"}] | {}         |

## <a name="next-steps"></a>後續步驟

* [建立和改變 Azure 儲存體或 Azure Data Lake 中的外部資料表](external-tables-azurestorage-azuredatalake.md)
* [建立和改變外部 SQL 資料表](external-sql-tables.md)
