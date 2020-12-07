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
ms.openlocfilehash: 6d85b5cbf6752924bbaa03d2b81732cd69be6239
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321619"
---
# <a name="capacity-policy"></a>產能原則 

容量原則可用來控制叢集中資料管理作業的計算資源。

## <a name="the-capacity-policy-object"></a>容量原則物件

容量原則的組成：

* [IngestionCapacity](#ingestion-capacity)
* [ExtentsMergeCapacity](#extents-merge-capacity)
* [ExtentsPurgeRebuildCapacity](#extents-purge-rebuild-capacity)
* [ExportCapacity](#export-capacity)
* [ExtentsPartitionCapacity](#extents-partition-capacity)

## <a name="ingestion-capacity"></a>內嵌容量

|屬性       |類型    |描述    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |叢集中並行內嵌作業數目的最大值。               |
|CoreUtilizationCoefficient         |double  |計算內嵌容量時要使用的核心百分比係數。 計算結果一律會由 `ClusterMaximumConcurrentOperations` <br> 叢集的內嵌容量總計（如下所 [示），](../management/diagnostics.md#show-capacity)其計算方式如下： <br> 最小 (`ClusterMaximumConcurrentOperations` ， `Number of nodes in cluster` * 最大 (1， `Core count per node`  *  `CoreUtilizationCoefficient`) # A3

> [!Note]
> 在具有三個或多個節點的叢集中，admin 節點不會參與內嵌作業。 `Number of nodes in cluster`會減少一。

## <a name="extents-merge-capacity"></a>範圍合併容量

|屬性                           |類型    |描述                                                                                                |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------------------|
|MinimumConcurrentOperationsPerNode |long    |單一節點上的並行範圍合併/重建作業數目的最少值。 預設值為1 |
|MaximumConcurrentOperationsPerNode |long    |單一節點上的並行範圍合併/重建作業數目的最大值。 預設值為3 |

叢集的總範圍合併容量（如下所示 [`.show capacity`](../management/diagnostics.md#show-capacity) ）是由下列計算：

`Number of nodes in cluster` X `Concurrent operations per node`

`Concurrent operations per node` `MinimumConcurrentOperationsPerNode` `MaximumConcurrentOperationsPerNode` 只要合併作業的成功率超過90%，就會在 [，] 範圍內由系統自動調整的有效值。

> [!Note]
> 在具有三個或多個節點的叢集中，admin 節點不會參與進行合併作業。 `Number of nodes in cluster`會減少一。

## <a name="extents-purge-rebuild-capacity"></a>範圍清除重建容量

|屬性                           |類型    |描述                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |單一節點上清除作業的並行重建範圍數目的最大值 |

叢集的總範圍清除重建容量 (如 [`.show capacity`](../management/diagnostics.md#show-capacity)) 的計算方式如下所示：

`Number of nodes in cluster` X `MaximumConcurrentOperationsPerNode`

> [!Note]
> 在具有三個或多個節點的叢集中，admin 節點不會參與進行合併作業。 `Number of nodes in cluster`會減少一。

## <a name="export-capacity"></a>匯出容量

|屬性                           |類型    |描述                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |叢集中並行匯出作業數目的最大值。                                           |
|CoreUtilizationCoefficient         |double  |計算匯出容量時要使用的核心百分比係數。 計算的結果一律會被標準化 `ClusterMaximumConcurrentOperations` 。 |

叢集的總匯出容量（如下所示 [`.show capacity`](../management/diagnostics.md#show-capacity) ）是以下列方式計算：

最小 (`ClusterMaximumConcurrentOperations` ， `Number of nodes in cluster` * 最大 (1， `Core count per node`  *  `CoreUtilizationCoefficient`) # A3

> [!Note]
> 在具有三個或更多節點的叢集中，系統管理員節點不會參與匯出作業。 `Number of nodes in cluster`會減少一。

## <a name="extents-partition-capacity"></a>範圍資料分割容量

|屬性                           |類型    |描述                                                                                         |
|-----------------------------------|--------|----------------------------------------------------------------------------------------------------|
|ClusterMinimumConcurrentOperations |long    |叢集中並行範圍分割作業數目的最少值。 預設值：1  |
|ClusterMaximumConcurrentOperations |long    |叢集中並行範圍分割作業數目的最大值。 預設值：16 |

叢集的總範圍資料分割容量 (如) 所示 [`.show capacity`](../management/diagnostics.md#show-capacity) 。

如果資料 `Concurrent operations` `ClusterMinimumConcurrentOperations` `ClusterMaximumConcurrentOperations` 分割作業的成功率超過90%，系統就會自動調整 [，] 範圍內的系統自動調整的有效值。

## <a name="materialized-views-capacity-policy"></a>具體化視圖容量原則

使用 [alter cluster policy 容量](capacity-policy.md#alter-cluster-policy-capacity)來變更容量原則。 這種變更需要 `AllDatabasesAdmin` 許可權。
原則可以用來變更具體化視圖的並行設定。 當叢集上定義了一個以上的具體化視圖，而且叢集無法跟上所有視圖的具體化時，可能需要進行這項變更。 依預設，並行設定相對較低，以確保具體化不會影響叢集的效能。

> [!WARNING]
> 只有當叢集的資源 (低 CPU、可用的記憶體) 時，才應該增加具體化視圖容量原則。 當資源受限時，增加這些值可能會導致資源耗盡，而不會影響叢集的效能。

具體化視圖容量原則是叢集 [容量原則](#capacity-policy)的一部分，且具有下列 JSON 標記法：

<!-- csl -->
``` 
{
   "MaterializedViewsCapacity": {
    "ClusterMaximumConcurrentOperations": 1,
    "ExtentsRebuildCapacity": {
      "ClusterMaximumConcurrentOperations": 50,
      "MaximumConcurrentOperationsPerNode": 5
    }
  }
}
```

### <a name="properties"></a>屬性

屬性 | 描述
|---|---|
|`ClusterMaximumConcurrentOperations` | 叢集可以同時具體化的具體化視圖數目上限。 根據預設，這個值是1，而具體化本身 (單一個別視圖) 可能會執行許多並行作業。 如果叢集上定義了一個以上的具體化視圖，而且如果叢集的資源處於良好狀態，建議您增加此值。 |
| `ExtentsRebuildCapacity`|  決定在具體化進程期間，針對所有具體化視圖執行的並行範圍重建作業數目。 如果有多個視圖同時執行，因為 `ClusterMaximumConcurrentOperation` 大於1，所以會共用這個屬性所定義的配額。 並行範圍重建作業的最大數目不會超過此值。 |

### <a name="extents-rebuild"></a>範圍重建

若要深入瞭解範圍重建作業，請參閱 [具體化視圖的運作方式](materialized-views/materialized-view-overview.md#how-materialized-views-work)。 範圍重建的最大數目是以下列方式計算：
    
```kusto
Maximum(`ClusterMaximumConcurrentOperations`, `Number of nodes in cluster` * `MaximumConcurrentOperationsPerNode`)
```

* 預設值為50並行重建總數和每個節點最多5個。
* `ExtentsRebuildCapacity`原則僅作為上限。 實際使用的值是由系統根據目前叢集的條件 (記憶體、CPU) 和重建作業所需的資源量估計來決定。 在實務上，平行存取可能會比容量原則中所指定的值更低。
    * 計量會 `MaterializedViewExtentsRebuild` 提供在每個具體化迴圈中重建的範圍數目的相關資訊。 如需詳細資訊，請參閱 [具體化監視](materialized-views/materialized-view-overview.md#materialized-views-monitoring)。

## <a name="defaults"></a>Defaults

預設容量原則具有下列 JSON 標記法：

```json
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MinimumConcurrentOperationsPerNode": 1,
    "MaximumConcurrentOperationsPerNode": 3
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  },
  "ExtentsPartitionCapacity": {
    "ClusterMinimumConcurrentOperations": 1,
    "ClusterMaximumConcurrentOperations": 16
  }
}
```

## <a name="control-commands"></a>控制命令

> [!WARNING]
> 更改容量原則之前，請洽詢 Azure 資料總管團隊。

* 用 [`.show cluster policy capacity`](capacity-policy.md#show-cluster-policy-capacity) 來顯示叢集目前的容量原則。

* 使用 [`.alter cluster policy capacity`](capacity-policy.md#alter-cluster-policy-capacity) 來改變叢集的容量原則。

## <a name="throttling"></a>節流

Kusto 會限制下列使用者起始命令的並行要求數目：

* 內嵌 (包含 [此處](../../ingest-data-overview.md) 列出的所有命令) 
   * 限制是在 [容量原則](#capacity-policy)中定義的。
* 清除
   * 全域目前是每個叢集一個固定的。
   * 清除重建容量會在內部使用，以判斷清除命令期間的並行重建作業數目。 因為此程式，清除命令不會遭到封鎖/節流，但會根據清除重建容量而更快或更慢。
* 出口
   * 限制是在 [容量原則](#capacity-policy)中定義的。

當叢集偵測到作業已超過允許的平行作業時，它會以429、「節流」、HTTP 程式碼回應。
請在部分輪詢之後重試此作業。
