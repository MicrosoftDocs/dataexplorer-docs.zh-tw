---
title: .創建-合併表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .create-合併表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 408046e198710c4b825a399fcb90960411de1041
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744439"
---
# <a name="create-merge-tables"></a>.建立-合併表

允許在單個批量操作中在特定資料庫的上下文中創建和/或擴展現有表的架構。

需要[資料庫使用者權限](../management/access-control/role-based-authorization.md),以及擴充現有表的[表管理員權限](../management/access-control/role-based-authorization.md)。

**語法**

`.create-merge``tables`*表名稱1([* 列名稱:欄類型],...)[`,` *表名稱2* ([列名稱:列類型], ...) ...

* 將建立不存在的指定表。
* 已存在的指定表將擴充其架構:
    * 現有表的_架構末尾將_添加不存在的列。
    * 命令中未指定的現有列不會從現有表的架構中刪除。
    * 在命令中指定與現有表架構中資料類型不同的現有列將導致失敗(不會創建或擴展任何表)。

**範例** 

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**傳回輸出**

| TableName | DatabaseName  | 資料夾 | 文件字串 |
|-----------|---------------|--------|-----------|
| 我的紀錄    | 頂部比較 |        |           |
| 我的使用者   | 頂部比較 |        |           |
