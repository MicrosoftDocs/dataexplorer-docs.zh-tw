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
ms.openlocfilehash: 7fa1ad900eb91390436e88c44ad9fd7394ad087d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348644"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

傳回執行查詢的目前主體名稱。

## <a name="syntax"></a>語法

`current_principal()`

## <a name="returns"></a>傳回

目前的主體完整名稱（FQN），其為 `string` 。  
字串格式為：  
*PrinciplaType* `=`*PrincipalId* `;`*TenantId*

## <a name="example"></a>範例

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
