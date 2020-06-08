---
title: 沙箱原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的沙箱原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 786f771878a7216b62dce127391f0e7967e954f6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512448"
---
# <a name="sandbox-policy"></a>沙箱原則

Azure 資料總管會在[沙箱](../concepts/sandboxes.md)內執行某些外掛程式，其中的可用資源受到限制，且可控制安全性和資源管理。

沙箱會在 Kusto 引擎的節點上執行。 其中一些限制定義于沙箱原則中，其中每個沙箱類型都可以有自己的原則。

沙箱原則會在叢集層級進行管理，並會影響叢集中的所有節點。

若要改變原則，您需要[AllDatabasesAdmin](../management/access-control/role-based-authorization.md)許可權。

## <a name="the-policy-object"></a>原則物件

沙箱原則具有下列屬性。

* **SandboxKind**：定義沙箱的類型（例如、 `PythonExecution` 、 `RExecution` ）。
* **IsEnabled**：定義此類型的沙箱是否可在叢集的節點上執行。
* **TargetCountPerNode**：定義允許在叢集節點上執行此類型的沙箱數目。
  * 值可以介於1到每個節點的處理器數目的兩倍之間。
  * 預設值為 16。
* **MaxCpuRatePerSandbox**：將最大 CPU 速率定義為單一沙箱可使用之所有可用核心的百分比。
  * 值可以介於1到100之間。
  * 預設值是 50。
* **MaxMemoryMbPerSandbox**：定義單一沙箱可以使用的最大記憶體數量（以 mb 為單位）。
  * 值可以介於200和65536（64GB）之間。
  * 預設值為20480（20 gb）。

## <a name="example"></a>範例

下列原則會針對和沙箱設定不同的限制 `PythonExecution` `RExecution` ：

```json
[
  {
    "SandboxKind": "PythonExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 4,
    "MaxCpuRatePerSandbox": 55,
    "MaxMemoryMbPerSandbox": 65536
  },
  {
    "SandboxKind": "RExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 2,
    "MaxCpuRatePerSandbox": 50,
    "MaxMemoryMbPerSandbox": 10240
  }
]
```

> [!NOTE]
> * 沙箱原則的變更會套用至從套用變更時開始建立的沙箱。 在原則變更之前預先配置的沙箱，將會根據先前的原則限制繼續執行，直到用來做為查詢的一部分為止。
> * 在原則的變更生效之前，可能會有最多五分鐘的延遲，因為叢集節點會定期輪詢原則變更。

## <a name="next-steps"></a>後續步驟

使用[沙箱原則控制命令](../management/sandbox-policy.md)來管理叢集的沙箱原則。
 
