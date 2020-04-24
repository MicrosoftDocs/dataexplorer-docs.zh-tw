---
title: ingestion_time （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 ingestion_time （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2474b8751be5cba2270bcbd2936c76b91f5f3ba0
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030033"
---
# <a name="ingestion_time"></a>ingestion_time()

抓取記錄的`$IngestionTime`隱藏`datetime`資料行，或為 null。
當`$IngestionTime`資料表的時，會自動定義資料行

::: zone pivot="azuredataexplorer"

已設定[IngestionTime 原則](../management/ingestiontimepolicy.md)（已啟用）。

::: zone-end

::: zone pivot="azuremonitor"

已設定 IngestionTime 原則（已啟用）。

::: zone-end

如果資料表未定義此原則，則會傳回 null 值。

這個函數必須在實際資料表的內容中使用，才能傳回相關資料。 例如，如果是在運算子之後叫用`summarize` ，則它會針對所有記錄傳回 null。

**語法**

 `ingestion_time()`

**傳回**

`datetime`值，指定在資料表中內嵌的大約時間。

**範例**

```kusto
T 
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
