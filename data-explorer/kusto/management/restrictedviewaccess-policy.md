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
ms.openlocfilehash: 33f21bdad11555ad2a55f285cbf40239236c561f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967599"
---
# <a name="restricted_view_access-policy-command"></a>restricted_view_access 原則命令

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