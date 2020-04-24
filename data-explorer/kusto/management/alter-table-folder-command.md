---
title: .alter 表資料夾 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .alter 表資料夾。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: a1d368d50f0e42acdbc25348b8025fbe8b530536
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522299"
---
# <a name="alter-table-folder"></a>.alter 資料表資料夾

更改現有表的資料夾值。 

`.alter``table`*表名*`folder`*資料夾*

> [!NOTE]
> * 需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)
> * 還允許最初建立表的[資料庫使用者](../management/access-control/role-based-authorization.md)對其進行編輯
> * 如果表不存在,則返回錯誤。 有關建立新表,請參閱[.create 表](create-table-command.md)

**範例** 

```
.alter table MyTable folder "Updated folder"
```

```
.alter table MyTable folder @"First Level\Second Level"
```