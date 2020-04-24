---
title: .撤銷放置表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .undo 刪除表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 55fd08ce2624c522b971e1ee3862f7d11c6f679f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519545"
---
# <a name="undo-drop-table"></a>.undo 捨棄資料表

`.undo`該`table`命令將放置表操作還原為特定`drop`的資料庫版本。

**語法**

`.undo``drop``table`表名 = 新表名稱 = DB_MajorVersion.DB_MinorVersion * * `as` `version=v` * * * *

命令必須使用資料庫上下文執行。

**傳回**

此命令：
* 返回原始表延伸區清單
* 指定範圍包含的紀錄
* 如果復原操作成功或失敗,則傳回
* 返回故障原因(如果相關)。

| 放大縮小字型功能 放大縮小字型功能                             | 記錄數 | 狀態                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | 恢復                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | 恢復                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | 無法修復範圍 | 範圍容器:4b47fd84-c7db-4cfb-9378-67c1de7bf154 未找到,範圍從存儲中刪除,無法還原 |

**範例**

```
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**如何尋找需要的資料庫版本**

您可以使用`.show``journal`指令 執行放置操作之前找到資料庫版本:

```
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| 原始實體版本 |
|-----------------------|
| v24.3                 |

**限制**

如果在此資料庫上執行了 Purge 命令,則撤銷放置表命令無法執行到清除執行之前的版本。

僅當尚未到達其駐留在的擴展區容器的硬刪除週期時,才能恢復範圍。

該指令需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)。