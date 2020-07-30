---
title: 縮小外掛程式-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的縮小外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e597a2467da21a2c9e83aba28a1e83b242f61c75
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346672"
---
# <a name="narrow-plugin"></a>narrow 外掛程式

```kusto
T | evaluate narrow()
```

外掛程式會將 `narrow` 寬型資料表「unpivots」成隻包含三個數據行的資料表：資料列編號、資料行類型和欄值（as `string` ）。

`narrow`外掛程式主要是為了顯示目的而設計，因為它可讓寬型資料表輕鬆顯示，而不需要水準捲軸。

## <a name="syntax"></a>語法

`T | evaluate narrow()`

## <a name="examples"></a>範例

下列範例顯示簡單的方式來讀取 Kusto `.show diagnostics` control 命令的輸出。

```kusto
.show diagnostics
 | evaluate narrow()
```

本身的結果 `.show diagnostics` 是具有單一資料列和33資料行的資料表。 藉由使用 `narrow` 外掛程式，我們將輸出「旋轉」為類似下面的內容：

資料列  | 資料行                              | 值
-----|-------------------------------------|-----------------------------
0    | IsHealthy                           | True
0    | IsRebalanceRequired                 | False
0    | IsScaleOutRequired                  | False
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
0    | IsDataWarmingRequired               | False
0    | RebalanceLastRunOn                  | 2017-03-21 09：14：53.8523455
0    | DataWarmingLastRunOn                | 2017-03-21 09：19：54.1438800
0    | MergesSuccessRate                   | 100
0    | NotHealthyReason                    | null
0    | IsAttentionRequired                 | False
0    | AttentionRequiredReason             | null
0    | ProductVersion                      | KustoRelease_2017。03.13。2
0    | FailedIngestOperations              | 0
0    | FailedMergeOperations               | 0
0    | MaxExtentsInSingleTable             | 64
0    | TableWithMaxExtents                 | KustoMonitoringPersistentDatabase.KustoMonitoringTable
0    | WarmExtentSize                      | 1779165230