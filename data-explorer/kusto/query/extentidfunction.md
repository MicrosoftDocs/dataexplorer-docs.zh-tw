---
title: 'extent_id ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 extent_id ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1f5584233a24c834e0ca6c28ed60aa5d7496b411
ms.sourcegitcommit: 284152eba9ee52e06d710cc13200a80e9cbd0a8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2020
ms.locfileid: "86291520"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

傳回唯一識別碼，識別目前記錄所在 ( 「範圍」 ) 的資料分區。

將此函數套用至未附加至資料分區的計算資料時，會傳回空的 guid (所有零) 。

**語法**

`extent_id()`

**傳回**

類型的值， `guid` 識別目前記錄的資料分區，或空的 guid (所有零) 。

**範例**

下列範例示範如何取得具有資料行之特定值的所有資料分區清單，其中包含一小時前的記錄 `ActivityId` 。 它會示範某些查詢運算子在這裡 (、 `where` 運算子，以及 `extend` `project`) 保留裝載記錄之資料分區的相關資訊。

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend eid=extent_id()
| summarize by eid
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
