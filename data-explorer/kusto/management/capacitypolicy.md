---
title: 容量原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的容量原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 4a2b18f28840291fc02002239297b45f38be00be
ms.sourcegitcommit: 4e46b497d518884693a142f4ae21ea497db81861
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83824912"
---
# <a name="capacity-policy"></a>產能原則

容量原則可用來控制用於叢集上資料管理作業的計算資源。

## <a name="the-capacity-policy-object"></a>容量原則物件

容量原則是由下列各項所組成：

* [IngestionCapacity](#ingestion-capacity)
* [ExtentsMergeCapacity](#extents-merge-capacity)
* [ExtentsPurgeRebuildCapacity](#extents-purge-rebuild-capacity)
* [ExportCapacity](#export-capacity)
* [ExtentsPartitionCapacity](#extents-partition-capacity)

## <a name="ingestion-capacity"></a>內嵌容量

|屬性                           |類型    |描述                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |叢集中並行內嵌作業數目的最大值                                                                                                            |
|CoreUtilizationCoefficient         |double  |計算內嵌容量時所用核心百分比的係數（計算的結果一律會正規化 `ClusterMaximumConcurrentOperations` ） |                                                                                                                             |

叢集的內嵌容量總計（如 [[顯示容量](../management/diagnostics.md#show-capacity)] 所示）是由下列計算：

最小值（ `ClusterMaximumConcurrentOperations` ， `Number of nodes in cluster` * 最大值（1， `Core count per node`  *  `CoreUtilizationCoefficient` ））

> [!Note]
> 在具有三個或更多節點的叢集中，admin 節點不會參與進行內嵌作業。 `Number of nodes in cluster`會減少一。

## <a name="extents-merge-capacity"></a>範圍合併容量

|屬性                           |類型    |描述                                                                                    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |單一節點上的並行範圍合併/重建作業數目的最大值 |

叢集的總範圍合併容量（如[顯示容量](../management/diagnostics.md#show-capacity)）的計算方式如下：

`Number of nodes in cluster`x`MaximumConcurrentOperationsPerNode`

> [!Note]
> * `MaximumConcurrentOperationsPerNode`會由系統在範圍 [1，5] 中自動調整，除非它已設定為較高的值。
> * 在具有三個或更多節點的叢集中，admin 節點不會參與執行合併作業。 `Number of nodes in cluster`會減少一。

## <a name="extents-purge-rebuild-capacity"></a>範圍清除重建容量

|屬性                           |類型    |描述                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |單一節點上的清除作業並行重建範圍數目的最大值 |

叢集的總範圍會清除重建容量（如顯示[容量](../management/diagnostics.md#show-capacity)），計算方式如下：

`Number of nodes in cluster`x`MaximumConcurrentOperationsPerNode`

> [!Note]
> 在具有三個或更多節點的叢集中，admin 節點不會參與執行合併作業。 `Number of nodes in cluster`會減少一。

## <a name="export-capacity"></a>匯出容量

|屬性                           |類型    |描述                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |叢集中並行匯出作業數目的最大值。                                                                                                           |
|CoreUtilizationCoefficient         |double  |計算匯出容量時所用核心百分比的係數。 計算的結果一律會以正規化 `ClusterMaximumConcurrentOperations` 。 |

叢集的總匯出容量（如 [[顯示容量](../management/diagnostics.md#show-capacity)] 所示）是由下列計算：

最小值（ `ClusterMaximumConcurrentOperations` ， `Number of nodes in cluster` * 最大值（1， `Core count per node`  *  `CoreUtilizationCoefficient` ））

> [!Note]
> 在具有三個或更多節點的叢集中，admin 節點不會參與執行匯出作業。 `Number of nodes in cluster`會減少一。

## <a name="extents-partition-capacity"></a>範圍分割區容量

|屬性                           |類型    |描述                                                                             |
|-----------------------------------|--------|----------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |叢集中並行範圍分割作業數目的最大值。 |

叢集的總範圍分割區容量（如 [[顯示容量](../management/diagnostics.md#show-capacity)] 所示）是由單一屬性所定義： `ClusterMaximumConcurrentOperations` 。

> [!Note]
> `ClusterMaximumConcurrentOperations`會由系統在範圍 [1，16] 中自動調整，除非它已設定為較高的值。

## <a name="defaults"></a>Defaults

預設容量原則具有下列 JSON 標記法：

```kusto 
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  }
}
```

## <a name="control-commands"></a>控制命令

> [!WARNING]
> 在改變容量原則之前，請洽詢 Azure 資料總管小組。

* 使用[。顯示叢集原則容量](capacity-policy.md#show-cluster-policy-capacity)，以顯示叢集目前的容量原則。

* 使用[. alter cluster policy 容量](capacity-policy.md#alter-cluster-policy-capacity)來改變叢集的容量原則。

## <a name="throttling"></a>節流

Kusto 會限制下列使用者起始命令的並行要求數目：

* 擷取（包括[此處](../management/data-ingestion/index.md)所列的所有命令）
   * [[容量原則](#capacity-policy)] 中定義的 [限制]。
* 清除
   * 全域目前已固定于每個叢集一個。
   * 清除的重建容量會在內部用來判斷清除命令期間並行重建作業的數目。 由於此程式的緣故，清除命令不會遭到封鎖/節流處理，但會根據清除重建容量而更快或更慢。
* 多餘
   * [[容量原則](#capacity-policy)] 中定義的 [限制]。

當叢集偵測到某個作業已超過允許的平行作業時，它會以429（「節流」） HTTP 程式碼回應。
請在部分輪詢之後重試作業。
