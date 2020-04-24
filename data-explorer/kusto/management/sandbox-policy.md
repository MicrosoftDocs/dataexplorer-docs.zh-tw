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
ms.openlocfilehash: 7ac5a92b2084eaf2b447f296be34b2f4e79e1bb7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520123"
---
# <a name="sandbox-policy"></a>沙箱原則

以下命令允許管理 Kusto 引擎服務中的[沙箱](../concepts/sandboxes.md)與[沙箱原則](sandboxpolicy.md)。

這些命令需要[所有資料庫管理員](access-control/role-based-authorization.md)許可權。

## <a name="sandbox-policy"></a>沙箱原則

### <a name="show-cluster-policy-sandbox"></a>.顯示叢集策略沙箱

在群集級別顯示所有已配置的沙箱策略。

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>.更改叢集策略沙箱

修改群集級別的沙箱策略集合。

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

### <a name="drop-cluster-policy-sandbox"></a>.刪除叢集策略沙箱

要刪除**所有**沙箱策略,請使用以下命令:

```kusto
.delete cluster policy sandbox
```

