---
title: 復原卸載資料表-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的復原卸載資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2682afaa9e589a8787b7e42a83e3bb68a24a2781
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616672"
---
# <a name="undo-drop-table"></a>.undo 捨棄資料表

`.undo`將 drop table 作業還原至特定的資料庫`drop` `table`版本。

**語法**

`.undo``drop` `as` *NewTableName* *TableName* `version=v` *DB_MajorVersion.DB_MinorVersion* TableName [NewTableName] DB_MajorVersion. DB_MinorVersion `table`

命令必須使用資料庫內容來執行。

**傳回**

此命令：
* 傳回原始資料表範圍清單
* 為每個範圍指定範圍包含的記錄數目
* 如果復原操作成功或失敗，則傳回
* 傳回失敗原因（如果相關）。

| ExtentId                             | NumberOfRecords | 狀態                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | ̈                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | ̈                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | 無法復原範圍 | 範圍容器：找不到4b47fd84-c7db-4cfb-9378-67c1de7bf154，已從儲存體移除範圍，且無法還原 |

**範例**

```kusto
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```kusto
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**如何尋找所需的資料庫版本**

您可以使用`.show` `journal`命令，在執行 drop 作業之前，找到資料庫版本：

```kusto
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| OriginalEntityVersion |
|-----------------------|
| v 24。3                 |

**限制**

如果在這個資料庫上執行了清除命令，「復原卸載資料表」命令就無法執行到較早的清除執行版本。

只有在尚未達到其所在之範圍容器的實刪除期間時，才可以復原範圍。

此命令需要[資料庫系統管理員許可權](../management/access-control/role-based-authorization.md)。
