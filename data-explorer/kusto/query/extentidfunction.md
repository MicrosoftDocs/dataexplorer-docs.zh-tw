---
title: extent_id （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 extent_id （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 300f7961fd11b433ef4e420d5a20b9ad9150b269
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737618"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

傳回唯一識別碼，識別目前記錄所在的資料分區（「範圍」）。 

將此函數套用至未附加至資料分區的計算資料，會傳回空的 guid （全部為零）。

**語法**

`extent_id()`

**傳回**

類型`guid`的值，識別目前記錄的資料分區或空的 guid （全部為零）。

**範例**

下列範例示範如何取得具有資料行`ActivityId`之特定值的所有資料分區清單，其中包含一小時前的記錄。 它會示範某些查詢運算子（在這裡是`where`運算子，但也適用于`extend`和`project`）會保留裝載記錄之資料分區的相關資訊。

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
