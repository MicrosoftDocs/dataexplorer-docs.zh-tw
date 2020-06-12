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
ms.openlocfilehash: 43e92edd74861acc8207a855243f9ec1e012070a
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717354"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

傳回執行查詢的目前主體名稱。

**語法**

`current_principal()`

**傳回**

目前的主體完整名稱（FQN），其為 `string` 。  
字串格式為：  
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
