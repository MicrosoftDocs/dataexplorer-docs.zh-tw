---
title: 沙箱原則-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的沙箱原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7d56d602f53db29f5ea558acb0e9e4288e5ac6e3
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967480"
---
# <a name="sandbox-policy-command"></a>沙箱原則命令

下列命令可讓您在 Kusto 引擎服務中管理[沙箱](../concepts/sandboxes.md)和[沙箱原則](sandboxpolicy.md)。

命令需要[AllDatabasesAdmin](access-control/role-based-authorization.md)許可權。

## <a name="sandbox-policy"></a>沙箱原則

### <a name="show-cluster-policy-sandbox"></a>。顯示叢集原則沙箱

顯示叢集層級上所有已設定的沙箱原則。

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

### <a name="drop-cluster-policy-sandbox"></a>。卸載叢集原則沙箱

若要卸載**所有**沙箱原則，請使用下列命令：

```kusto
.delete cluster policy sandbox
```

