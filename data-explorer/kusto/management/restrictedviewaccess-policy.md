---
title: Kusto RestrictedViewAccess 原則管理-Azure 資料總管
description: 瞭解 Azure 資料總管中的 RestrictedViewAccess 原則命令。 請參閱如何查看、啟用、停用、變更和刪除此原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9ec328e3a15af3cedb833354f7e8ecea0fdc22c8
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002916"
---
# <a name="restricted_view_access-policy-command"></a>restricted_view_access 原則命令

*RestrictedViewAccess*原則記載于[此處](../management/restrictedviewaccesspolicy.md)。

在資料庫的資料表 (s) 上啟用或停用原則的控制命令如下：

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

若要刪除原則 (相當於停用) ：
```kusto
.delete table TableName policy restricted_view_access  
```
