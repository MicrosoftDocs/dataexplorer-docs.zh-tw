---
title: 外部資料表控制項命令-Azure 資料總管
description: 本文說明 Azure 資料總管中的外部資料表控制命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 580f675360b96d56d43e1100cbba97d09a95c945
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227701"
---
# <a name="external-table-control-commands"></a>外部資料表控制項命令

如需外部資料表的總覽，請參閱[外部資料表](../query/schema-entities/externaltables.md)。 

## <a name="common-external-tables-control-commands"></a>一般外部資料表控制項命令

下列命令與_任何_外部資料表相關（任何類型）。

## <a name="show-external-tables"></a>：顯示外部資料表

* 傳回資料庫中的所有外部資料表（或特定的外部資料表）。
* 需要[資料庫監視器許可權](../management/access-control/role-based-authorization.md)。

**語法：** 

`.show` `external` `tables`

`.show``external` `table` *TableName*

**輸出**

| 輸出參數 | 類型   | 說明                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | 字串 | 外部資料表的名稱                                             |
| TableType        | 字串 | 外部資料表的類型                                              |
| 資料夾           | 字串 | 資料表的資料夾                                                     |
| DocString        | 字串 | 記錄資料表的字串                                       |
| [內容]       | 字串 | 資料表的 JSON 序列化屬性（特定于資料表類型） |


**範例：**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | 資料夾         | DocString | [內容] |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | ExternalTables | Docs      | {}         |


## <a name="show-external-table-schema"></a>：顯示外部資料表架構

* 以 JSON 或 CSL 的形式傳回外部資料表的架構。 
* 需要[資料庫監視器許可權](../management/access-control/role-based-authorization.md)。

**語法：** 

`.show``external` `table` *TableName* `schema` `as` （ `json`  |  `csl` ）

`.show``external` `table` *TableName*`cslschema`

**輸出**

| 輸出參數 | 類型   | 說明                        |
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

**輸出**

*json*

| TableName | 結構描述    | DatabaseName | 資料夾         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name"： "ExternalBlob"，<br>"Folder"： "ExternalTables"，<br>"DocString"： "檔"，<br>"OrderedColumns"： [{"Name"： "x"，"Type"： "System.object"，"CslType"： "long"，"DocString"： ""}，{"Name"： "s"，"Type"： "system.string"，"CslType"： ""}]} | DB           | ExternalTables | Docs      |


*csl*

| TableName | 結構描述          | DatabaseName | 資料夾         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long,s:string | DB           | ExternalTables | Docs      |

## <a name="drop-external-table"></a>. drop external table

* 卸載外部資料表 
* 無法在此作業之後還原外部資料表定義
* 需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)。

**語法：**  

`.drop``external` `table` *TableName*

**輸出**

傳回已卸載之資料表的屬性。 請參閱[。顯示外部資料表](#show-external-tables)。

**範例：**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | 資料夾         | DocString | 結構描述       | [內容] |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | ExternalTables | Docs      | [{"Name"： "x"，"CslType"： "long"}，<br> {"Name"： "s"，"CslType"： "string"}] | {}         |

