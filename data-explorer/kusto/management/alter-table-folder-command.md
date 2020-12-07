---
title: 。 alter table 資料夾-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 alter table 資料夾。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 8ba9eb06e07dd513d20cdade87322d9ef5f17d24
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321636"
---
# <a name="alter-table-folder"></a>.alter 資料表資料夾

改變現有資料表的資料夾值。 

`.alter``table` *TableName* `folder` *資料夾*

> [!NOTE]
> * 需要 [資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 原本建立資料表的 [資料庫使用者](../management/access-control/role-based-authorization.md) 也可以編輯它
> * 如果資料表不存在，則會傳回錯誤。 若要建立新的資料表，請參閱 [`.create table`](create-table-command.md)

**範例** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```