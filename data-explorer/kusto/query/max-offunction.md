---
title: max_of （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 max_of （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4b912b1bdc68d7b3071ace8547f0aaf7c679a86a
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271599"
---
# <a name="max_of"></a>max_of()

傳回數個已評估之數值運算式的最大值。

```kusto
max_of(10, 1, -3, 17) == 17
```

**語法**

`max_of``(` *expr_1* `,` *expr_2* .。。`)`

**引數**

* *expr_i*：要評估的純量運算式。

- 所有引數都必須是相同的類型。
- 最多支援64個引數。

**傳回**

所有引數運算式的最大值。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|
