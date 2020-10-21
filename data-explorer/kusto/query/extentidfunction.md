---
title: 'extent_id ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 extent_id。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: eb1984ba80b5d2940591428fea4b1f6c3982f9d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246630"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

傳回唯一識別碼，識別目前記錄所在 ( 「範圍」 ) 的資料分區。

將此函數套用至未附加至資料分區的計算資料時，會傳回空的 guid (所有零) 。

## <a name="syntax"></a>語法

`extent_id()`

## <a name="returns"></a>傳回

類型的值， `guid` 識別目前記錄的資料分區，或 (所有零) 的空白 guid。

## <a name="example"></a>範例

下列範例顯示如何取得所有資料分區的清單，其中包含一小時前的記錄，以及資料行的特定值 `ActivityId` 。 它會示範某些查詢運算子在這裡 (、 `where` 運算子，以及 `extend` `project`) 保留裝載記錄之資料分區的相關資訊。

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
