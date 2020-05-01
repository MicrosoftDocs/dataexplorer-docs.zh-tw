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
ms.openlocfilehash: f03a2927d3f0a4fe7ee1719594f2d1f19e231d0f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618355"
---
# <a name="alter-table-folder"></a>.alter 資料表資料夾

改變現有資料表的資料夾值。 

`.alter``table` *TableName* TableName `folder` *資料夾*

> [!NOTE]
> * 需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 原先建立資料表的[資料庫使用者](../management/access-control/role-based-authorization.md)也可以編輯它
> * 如果資料表不存在，則會傳回錯誤。 如需建立新的資料表，請參閱[建立資料表](create-table-command.md)

**範例** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```