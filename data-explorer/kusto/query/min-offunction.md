---
title: min_of （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 min_of （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0762b1416df32279b9801c47f129a6966772a7e2
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271361"
---
# <a name="min_of"></a>min_of()

傳回數個已評估之數值運算式的最小值。

```kusto
min_of(10, 1, -3, 17) == -3
```

**語法**

`min_of``(` *expr_1* `,` *expr_2* .。。`)`

**引數**

* *expr_i*：要評估的純量運算式。

- 所有引數都必須是相同的類型。
- 最多支援64個引數。

**傳回**

所有引數運算式的最小值。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|
