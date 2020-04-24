---
title: 沙箱策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的沙箱策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0a59e25c6c38d3189330299af1b19f89357cb456
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520055"
---
# <a name="sandbox-policy"></a>沙箱原則

## <a name="overview"></a>概觀

Kusto 支援在[沙箱](../concepts/sandboxes.md)中運行某些外掛程式,其中沙箱可用的資源受到限制和控制,無論是出於安全考慮,還是為了資源治理目的。

沙箱在 Kusto 引擎服務的節點上運行,其某些限制在沙箱策略中定義,其中每個沙箱類型都可以有自己的沙箱策略。

沙箱策略在群集級別進行管理,並影響群集中的所有節點。

更改策略需要[所有資料庫管理員](../management/access-control/role-based-authorization.md)許可權。

## <a name="the-policy-object"></a>原則物件

沙箱原則具有以下屬性:

* **沙箱金德**:定義沙箱的種類(例如`PythonExecution` `RExecution`, .
* **啟用**:定義是否啟用此類沙箱在群集的節點上運行。
* **TargetCountPerNode**:定義允許在群集的節點上運行此類沙箱的數量。
  * 值可以是每個節點處理器數量的 1 到兩倍。
  * 預設值是 `16`。
* **MaxCpuRatePerSandbox**:定義單個沙盒可以使用的所有可用內核的最大 CPU 速率百分比。
  * 值可以介於 1 和 100 之間。
  * 預設值是 `50`。
* **MaxMemoryMbPerSandbox:** 定義單個沙箱可以使用的最大內存量(以兆位元組為單位)。
  * 值可以在 200 和 65536 (64GB) 之間。
  * 默認值為`20480`(20GB)。

## <a name="example"></a>範例

以下政策為 2 種不同類型的沙箱設定不同的`PythonExecution``RExecution`限制 - 和 :

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

## <a name="notes"></a>注意

* 對沙箱策略的更改應用於從應用更改時開始創建的 sanbox。
  * 在策略更改之前預先分配的沙箱將繼續根據以前的策略限制運行,直到它們用作查詢的一部分。
* 由於群集節點定期輪詢策略更改,因此在策略更改生效之前,可能會延遲最多 5 分鐘。

## <a name="next-steps"></a>後續步驟

使用[沙箱策略控制命令](../management/sandbox-policy.md)來管理群集的沙箱策略。