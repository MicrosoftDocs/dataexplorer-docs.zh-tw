---
title: 容量原則控制命令-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的容量原則控制命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 512ab14c2abc1f777376d81d4944caf2c3343513
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967327"
---
# <a name="capacity-policy-commands"></a>容量原則命令

下列控制命令可用於管理叢集的[容量原則](../management/capacitypolicy.md)。

命令需要[AllDatabasesAdmin](../management/access-control/role-based-authorization.md)許可權。

## <a name="show-cluster-policy-capacity"></a>顯示叢集原則容量

```kusto
.show cluster policy capacity
```

顯示叢集目前的容量原則。

**輸出**

|原則名稱 | 實體名稱 | 原則 | 子實體 | 實體類型
|---|---|---|---|---
|CapacityPolicy | | JSON 格式的字串，代表原則 | 叢集中的資料庫清單 |叢集


## <a name="alter-cluster-policy-capacity"></a>改變叢集原則容量

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**注意**：叢集容量原則的變更可能需要最多1小時的時間才會生效。

**範例：**

* 明確改變叢集原則的所有屬性：

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

* 改變叢集層級原則的單一屬性，讓其他所有屬性保持不變：

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
