---
title: extent_tags() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的extent_tags()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d8af7e51c5e2efb16763541db05e9ccc7e2cb95f
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765420"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

返回包含當前記錄駐留在的數據分片("範圍")[標記](../management/extents-overview.md#extent-tagging)的動態數位。 

將此函數應用於未附加到數據分片的計算數據將返回一個空值。

**語法**

`extent_tags()`

**傳回**

類型`dynamic`的值,是包含當前記錄的擴展區標記的陣列或空值。

**範例**

下面的範例展示如何獲取包含一小時前記錄的所有資料分片的標記以及列`ActivityId`的特定值。 它演示了某些查詢運算符(此處為`where`運算符,但對於`extend`和`project`) 也是如此,請保留有關承載記錄的數據分片的資訊。

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

下面的示例演示如何從最後一小時獲取所有記錄的計數,這些記錄存儲在使用標記`MyTag`(和可能的其他標記)標記的區位區中,但未使用標`drop-by:MyOtherTag`記 進行標記。

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
