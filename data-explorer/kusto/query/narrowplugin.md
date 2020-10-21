---
title: 窄外掛程式-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的窄外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a27794647eed3e8b30533d73456a0b1fb8ccde6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243197"
---
# <a name="narrow-plugin"></a>narrow 外掛程式

```kusto
T | evaluate narrow()
```

外掛程式會將 `narrow` 寬型資料表「unpivots」為只有三個數據行的資料表：資料列編號、資料行類型和資料行值 (為 `string`) 。

`narrow`外掛程式的設計主要是為了顯示用途，因為它可讓您輕鬆地顯示寬型資料表，而不需要水準滾動。

## <a name="syntax"></a>語法

`T | evaluate narrow()`

## <a name="examples"></a>範例

下列範例顯示一個簡單的方法來讀取 Kusto control 命令的輸出 `.show diagnostics` 。

```kusto
.show diagnostics
 | evaluate narrow()
```

本身的結果 `.show diagnostics` 是具有單一資料列和33資料行的資料表。 藉由使用 `narrow` 外掛程式，我們會將輸出「旋轉」為如下的內容：

資料列  | 資料行                              | 值
-----|-------------------------------------|-----------------------------
0    | IsHealthy                           | 是
0    | IsRebalanceRequired                 | 否
0    | IsScaleOutRequired                  | 否
0    | MachinesTotal                       | 2
0    | MachinesOffline                     | 0
0    | NodeLastRestartedOn                 | 2017-03-14 10：59：18.9263023
0    | AdminLastElectedOn                  | 2017-03-14 10：58：41.6741934
0    | ClusterWarmDataCapacityFactor       | 0.130552847673333
0    | ExtentsTotal                        | 136
0    | DiskColdAllocationPercentage        | 5
0    | InstancesTargetBasedOnDataCapacity  | 2
0    | TotalOriginalDataSize               | 5167628070
0    | TotalExtentSize                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | IngestionsInProgress                | 0
0    | IngestionsSuccessRate               | 100
0    | MergesInProgress                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | BuildTime                           | 2017-03-13 11：02：44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | IsDataWarmingRequired               | 否
0    | RebalanceLastRunOn                  | 2017-03-21 09：14：53.8523455
0    | DataWarmingLastRunOn                | 2017-03-21 09：19：54.1438800
0    | MergesSuccessRate                   | 100
0    | NotHealthyReason                    | ;
0    | IsAttentionRequired                 | 否
0    | AttentionRequiredReason             | ;
0    | ProductVersion                      | KustoRelease_2017. 03.13。2
0    | FailedIngestOperations              | 0
0    | FailedMergeOperations               | 0
0    | MaxExtentsInSingleTable             | 64
0    | TableWithMaxExtents                 | KustoMonitoringPersistentDatabase.KustoMonitoringTable
0    | WarmExtentSize                      | 1779165230