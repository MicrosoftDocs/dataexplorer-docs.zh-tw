---
title: current_principal() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的current_principal()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 36cebef7db3042bb59ccc5c7c25a56b2c1a661dc
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766032"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

返回運行查詢的當前主體名稱。

**語法**

`current_principal()`

**傳回**

目前主體完全限定名稱 (FQN)`string`作為 。  
字串形成為:  
*普林西貝拉類型*`=`*主體租戶*`;`*Id*

**範例**

```kusto
print fqn=current_principal()
```

|fqn|
|---|
|aaduser_346e950e-4a62-42bf-96f5-4cf4eac3f11e;72f988bf-86f1-41af-91ab-2d7cd011db47|

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
