---
title: extent_tags （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 extent_tags （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f745d9cb180842e86c184a24ed24c4e2f024f129
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348117"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

傳回動態陣列，其中包含目前記錄所在之資料分區（「範圍」）的[標記](../management/extents-overview.md#extent-tagging)。 

將此函數套用至未附加至資料分區的計算資料會傳回空值。

## <a name="syntax"></a>語法

`extent_tags()`

## <a name="returns"></a>傳回

類型的值， `dynamic` 這是保留目前記錄範圍標籤的陣列，或空白值。

## <a name="examples"></a>範例

下列範例顯示如何取得清單，其中包含一小時前具有資料行之特定值的所有資料分區標記 `ActivityId` 。 它會示範某些查詢運算子（在這裡 `where` 是運算子，但也適用于 `extend` 和）會 `project` 保留裝載記錄之資料分區的相關資訊。

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

下列範例示範如何取得過去一小時內所有記錄的計數（儲存在標有標記的範圍中 `MyTag` （也可能是其他標記），但不會以標記標記） `drop-by:MyOtherTag` 。

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
