---
title: 容量策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的容量策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: af648bd0a4b328477b14e20a2457e3e914df2827
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521993"
---
# <a name="capacity-policy"></a>容量原則

容量策略用於控制用於執行資料引入和其他資料整理操作(如合併擴展區)的計算資源。

## <a name="the-capacity-policy-object"></a>容量策略物件

`IngestionCapacity`容量策略由和`ExtentsMergeCapacity``ExtentsPurgeRebuildCapacity`組成。 `ExportCapacity`

### <a name="ingestion-capacity"></a>攝取能力

|屬性                           |類型    |描述                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|叢集最大併發操作 |long    |叢集並發引入操作數的最大值                                                                                                            |
|核心利用率係數         |double  |在計算攝入容量時要利用的內核百分比的係數(計算結果將始終按`ClusterMaximumConcurrentOperations`) 規範化。 |                                                                                                                             |

叢集的總引入容量(如[.show 容量](../management/diagnostics.md#show-capacity)所示)的計算方式如下:

最小值`ClusterMaximumConcurrentOperations`(=`Number of nodes in cluster`最大值(1,)) `Core count per node`  *  `CoreUtilizationCoefficient`

> [!Note] 
> 在具有三個或三個節點以上的群集中,管理節點不參與執行引入操作,因此`Number of nodes in cluster`減少了 1。

### <a name="extents-merge-capacity"></a>擴充區合併容量

|屬性                           |類型    |描述                                                                                    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------|
|最大併發操作 PerNode |long    |單個節點上的併發擴展盤區合併/重建操作數的最大值 |

叢集的總延伸盤區合併容量(如[.show 容量](../management/diagnostics.md#show-capacity)所示)的計算方式如下:

`Number of nodes in cluster`X.`MaximumConcurrentOperationsPerNode`

> [!Note] 
> 在具有三個或三個節點以上的群集中,管理節點不參與執行合併操作,因此`Number of nodes in cluster`減少了 1。

### <a name="extents-purge-rebuild-capacity"></a>延伸區清除重建容量

|屬性                           |類型    |描述                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|最大併發操作 PerNode |long    |單一節點上並發擴展重建操作(清除操作的重建延伸範圍)的最大值 |

叢集的總延伸範圍清除重建容量(如[.show 容量](../management/diagnostics.md#show-capacity)所示)的計算方式如下:

`Number of nodes in cluster`X.`MaximumConcurrentOperationsPerNode`

> [!Note] 
> 在具有三個或三個節點以上的群集中,管理節點不參與執行合併操作,因此`Number of nodes in cluster`減少了 1。

### <a name="export-capacity"></a>出口能力

|屬性                           |類型    |描述                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|叢集最大併發操作 |long    |群集中併發匯出操作數的最大值。                                                                                                           |
|核心利用率係數         |double  |計算匯出容量時要利用的型芯百分比的係數(計算結果將始終按`ClusterMaximumConcurrentOperations`) 規範化。 |

叢集的總匯出容量(如[.show 容量](../management/diagnostics.md#show-capacity)所示)的計算方式如下:

最小值`ClusterMaximumConcurrentOperations`(=`Number of nodes in cluster`最大值(1,)) `Core count per node`  *  `CoreUtilizationCoefficient`

> [!Note] 
> 在具有三個或三個節點以上的群集中,管理節點不參與執行匯出操作,因此`Number of nodes in cluster`減少了 1。

### <a name="extents-partition-capacity"></a>延伸區分割區容量

|屬性                           |類型    |描述                                                                             |
|-----------------------------------|--------|----------------------------------------------------------------------------------------|
|叢集最大併發操作 |long    |群集中併發區分區操作數的最大值。 |

叢集的總延伸區分割區容量(如[.show 容量](../management/diagnostics.md#show-capacity)的名稱) 由單`ClusterMaximumConcurrentOperations`個屬性定義: 。

### <a name="defaults"></a>Defaults

預設容量策略具有以下 JSON 表示形式:

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

> [!WARNING]
> **很少建議**在不事先諮詢 Kusto 團隊的情況下更改容量策略。

## <a name="control-commands"></a>控制命令

* 使用[.show 叢集策略容量](capacity-policy.md#show-cluster-policy-capacity)來顯示叢集的目前容量策略。
* 使用[.alter 群集策略容量](capacity-policy.md#alter-cluster-policy-capacity)更改群集的容量策略。

## <a name="throttling"></a>節流

Kusto 限制以下指令的併發請求數:

1. 引入(包括[此處](../management/data-ingestion/index.md)列出的所有命令)
      * 限制在[容量策略](#capacity-policy)中定義。
1. 合併
      * 限制在[容量策略](#capacity-policy)中定義。
1. 清除
      * 全域當前固定在每個群集的一個。
      * 清除重建容量在內部用於確定清除命令期間的併發重建操作數(清除命令不會因此被阻止/限制,但會根據清除重建容量更快地/變慢)。
1. 出口
      * 限制在[容量策略](#capacity-policy)中定義。


當 Kusto 檢測到某些操作已超過允許的併發操作時,Kusto 將使用 429 HTTP 代碼進行回應。
用戶端應在一些回退後重試該操作。