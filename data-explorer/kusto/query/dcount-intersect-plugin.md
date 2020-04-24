---
title: dcount_intersect外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的dcount_intersect外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7771456ffa75085c79933c2e789e3d98f352b76f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516128"
---
# <a name="dcount_intersect-plugin"></a>dcount_intersect外掛程式

基於 hll 值(N 在 [2..16] 的範圍內)計算 N 個集之間的交集,並返回 N 個 dcount 值。

給定集 S<sub>1</sub>, S<sub>2</sub>. . S<sub>n</sub> - 傳回數值將表示不同的計數:  
S<sub>1</sub>, S<sub>1</sub> • S<sub>2</sub>,  
S<sub>1</sub> • S<sub>2</sub> • S<sub>3</sub>,  
... ,  
S<sub>1</sub> 、 S<sub>2</sub> 2 "S<sub>n</sub>

    T | evaluate dcount_intersect(hll_1, hll_2, hll_3)

**語法**

*T* `dcount_intersect(` *hll_1*`,` *hll_2* *hll_3* `,` T hll_1 , hll_2 , [ hll_3 ... `| evaluate``)`

**引數**

* *T*: 輸入表格表示式。
* *hll_i*: 使用[hll()](./hll-aggfunction.md)函數計算的 Set<sub>Si</sub>的值。

**傳回**

返回具有 N dcount 值的表(每列,表示集交點)。
列名稱為 s0、s1、...(直到 n-1)。

**範例**

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

|偶數|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|