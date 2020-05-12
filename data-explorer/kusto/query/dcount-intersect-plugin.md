---
title: dcount_intersect 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 dcount_intersect 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c431f17184570b294b9c8077028ac792719b4abd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225202"
---
# <a name="dcount_intersect-plugin"></a>dcount_intersect 外掛程式

根據值計算 N 個集合之間 `hll` 的交集（[2.. 16] 範圍內的 n 個），並傳回 n 個 `dcount` 值。

提供的設定為 S<sub>1</sub>、s<sub>2</sub>、.。 S<sub>n</sub> -傳回值將代表的相異計數：  
S<sub>1</sub>，s<sub>1</sub> ∩ s<sub>2</sub>，  
S<sub>1</sub> ∩ s<sub>2</sub> ∩ s<sub>3</sub>，  
... ,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩ .。。∩ S<sub>n</sub>

    T | evaluate dcount_intersect(hll_1, hll_2, hll_3)

**語法**

*T* `| evaluate` `dcount_intersect(` *hll_1*、 *hll_2*、[ `,` *hll_3* `,` ...]`)`

**引數**

* *T*：輸入表格式運算式。
* *hll_i*：<sub>我</sub>以 Function 計算的 set S 值 [`hll()`](./hll-aggfunction.md) 。

**傳回**

傳回具有 N `dcount` 個值的資料表（每個資料行代表集合交集）。
資料行名稱為 s0、s1 .。。（直到 n-1）。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// Generate numbers from 1 to 100
range x from 1 to 100 step 1
| extend isEven = (x % 2 == 0), isMod3 = (x % 3 == 0), isMod5 = (x % 5 == 0)
// Calculate conditional HLL values (note that '0' is included in each of them as additional value, so we will subtract it later)
| summarize hll_even = hll(iif(isEven, x, 0), 2),
            hll_mod3 = hll(iif(isMod3, x, 0), 2),
            hll_mod5 = hll(iif(isMod5, x, 0), 2) 
// Invoke the plugin that calculates dcount intersections         
| evaluate dcount_intersect(hll_even, hll_mod3, hll_mod5)
| project evenNumbers = s0 - 1,             //                             100 / 2 = 50
          even_and_mod3 = s1 - 1,           // gcd(2,3) = 6, therefor:     100 / 6 = 16
          even_and_mod3_and_mod5 = s2 - 1   // gcd(2,3,5) is 30, therefore: 100 / 30 = 3 
```

|evenNumbers|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|