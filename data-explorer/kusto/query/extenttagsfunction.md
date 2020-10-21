---
title: 'extent_tags ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 extent_tags。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: da705d558a09bdcc52bf07fc807e53fdccb9396c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249083"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

傳回動態陣列，其中含有目前記錄所在 ( 「範圍」 ) 資料分區的 [標記](../management/extents-overview.md#extent-tagging) 。 

將此函數套用至未附加至資料分區的計算資料時，會傳回空值。

## <a name="syntax"></a>語法

`extent_tags()`

## <a name="returns"></a>傳回

型別的值， `dynamic` 這個值是保存目前記錄之範圍標籤的陣列，或空白值。

## <a name="examples"></a>範例

下列範例示範如何取得具有資料行之特定值的所有資料分區，以及資料行之特定值的標記 `ActivityId` 。 它會示範某些查詢運算子 (在這裡， `where` 也就是運算子，但這也適用于 `extend` 和 `project`) 保留裝載記錄之資料分區的相關資訊。

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

下列範例會示範如何取得過去一小時內的所有記錄計數，這些記錄會儲存在標記 (標籤的範圍中， `MyTag` 而且可能有其他標記) ，但未標記標記 `drop-by:MyOtherTag` 。

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
