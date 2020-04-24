---
title: 容量策略控制命令 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的容量策略控制命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 929cfa885a7373b400b832d908677a7a5fb93ef6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522078"
---
# <a name="capacity-policy-control-commands"></a>容量原則控制命令

以下控制命令可用於管理叢集的[容量原則](../management/capacitypolicy.md)。

這些命令需要[所有資料庫管理員](../management/access-control/role-based-authorization.md)許可權。

## <a name="show-cluster-policy-capacity"></a>顯示叢集政策容量

```kusto
.show cluster policy capacity
```

顯示群集的當前容量策略。

**輸出**

|原則名稱 | 實體名稱 | 原則 | 子實體 | 實體類型
|---|---|---|---|---
|容量政策 | | JSON 格式的字串,表示策略 | 群組的資料庫清單 |叢集


## <a name="alter-cluster-policy-capacity"></a>變更叢集政策容量

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**注意**:對群集容量策略的更改最多可能需要 1 小時才能生效。

**範例：**

* 明確變更叢集政策的屬性:

```kusto
.alter cluster policy capacity
'{'
  '"IngestionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 512,'
    '"CoreUtilizationCoefficient": 0.75'
  '},'
  '"ExtentsMergeCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExtentsPurgeRebuildCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExportCapacity": {'
    '"ClusterMaximumConcurrentOperations": 100,'
    '"CoreUtilizationCoefficient": 0.25'
  '},'
  '"ExtentsPartitionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 4'
  '}'
'}'
```

* 變更叢集級策略的單個屬性,保持所有其他屬性不變:

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
