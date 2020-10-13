---
title: 沙箱原則-Azure 資料總管 |Microsoft Docs
description: 瞭解 Azure 資料總管中的沙箱原則命令。 瞭解如何顯示、調整和捨棄沙箱原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ba37147db87c436aff7d77790641b17576e86392
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002962"
---
# <a name="sandbox-policy-command"></a>沙箱原則命令

下列命令可讓您在 Kusto 引擎服務中管理 [沙箱](../concepts/sandboxes.md) 和 [沙箱原則](sandboxpolicy.md) 。

這些命令需要 [AllDatabasesAdmin](access-control/role-based-authorization.md) 許可權。

## <a name="sandbox-policy"></a>沙箱原則

### <a name="show-cluster-policy-sandbox"></a>。顯示叢集原則沙箱

顯示叢集層級上所有設定的沙箱原則。

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>。 alter cluster policy 沙箱

修改叢集層級的沙箱原則集合。

```kusto
.alter cluster policy sandbox @'['
  '{'
    '"SandboxKind": "PythonExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '},'
  '{'
    '"SandboxKind": "RExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '}'
']'
```

### <a name="drop-cluster-policy-sandbox"></a>. drop cluster policy 沙箱

若要卸載 **所有** 的沙箱原則，請使用下列命令：

```kusto
.delete cluster policy sandbox
```
