---
title: current_principal （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 current_principal （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b1d45fb8b0749a4be30854dd9b0120a7eb127bf2
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227293"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

傳回執行查詢的目前主體名稱。

**語法**

`current_principal()`

**傳回**

目前的主體完整名稱（FQN），其為 `string` 。  
此字串的格式如下：  
*PrinciplaType* `=`*PrincipalId* `;`*TenantId*

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print fqn=current_principal()
```

|fqn|
|---|
|以 aaduser name> = 346e950e-4a62-42bf-96f5-4cf4eac3f11e; 72f988bf-86f1-41af-91ab-2d7cd011db47|

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
