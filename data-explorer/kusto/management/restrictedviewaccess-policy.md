---
title: Kusto RestrictedViewAccess 原則管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的 RestrictedViewAccess 原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9da59a53819396cf2cbd522f4a1e1296f585bf2f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617542"
---
# <a name="restrictedviewaccess-policy"></a>RestrictedViewAccess 原則

*RestrictedViewAccess*原則記載于[此處](../management/restrictedviewaccesspolicy.md)。

在資料庫的資料表上啟用或停用原則的控制命令如下：

若要啟用/停用原則：
```kusto
.alter table TableName policy restricted_view_access true|false
```

若要啟用/停用多個資料表的原則：
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

若要查看原則：
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

若要刪除原則（相當於停用）：
```kusto
.delete table TableName policy restricted_view_access  
```