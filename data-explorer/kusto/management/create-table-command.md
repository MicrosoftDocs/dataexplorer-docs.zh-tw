---
title: .創建表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .create 表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 1c84b9b6cec5a5bb7ab620871f59c188bf71cef4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744246"
---
# <a name="create-table"></a>.create 資料表

建立新的空表。

該命令必須在特定資料庫的上下文中運行。

需要[資料庫使用者權限](../management/access-control/role-based-authorization.md)。

**語法**

`.create``table`*表名稱*([列名稱:列類型],...) 【`with` `(` `,``folder``=`*FolderName* `)` *Documentation*% 文件 [ 資料夾名稱 】 `docstring` `=`

如果表已存在,該命令將返回成功。

**範例** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**傳回輸出**

以 JSON 格式傳回表的架構,與以下格式相同:

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> 要創建多個表,請使用[.create 表](/create-tables.md)命令來提高群集的性能和更低的負載。

## <a name="create-merge-table"></a>.建立-合併表

創建新錶或擴展現有表。 

該命令必須在特定資料庫的上下文中運行。 

需要[資料庫使用者權限](../management/access-control/role-based-authorization.md)。

**語法**

`.create-merge``table`*表名稱*([列名稱:列類型],...) 【`with` `(` `,``folder``=`*FolderName* `)` *Documentation*% 文件 [ 資料夾名稱 】 `docstring` `=`

如果表不存在,則完全與".create 表"命令一樣運行。

如果表 T 存在,並且傳送「.create-<columns specification>合併表 T ( )」命令,則:

* T<columns specification>中 以前不存在的任何列都將添加到 T 架構的末尾。
* 不會<columns specification>從 T 中刪除 T 中的任何欄位。
* T<columns specification>存在但數據類型不同的的任何列將導致命令失敗。
