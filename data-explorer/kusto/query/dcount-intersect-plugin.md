---
title: dcount_intersect 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 dcount_intersect 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7c66647dcdebfc86990c9a63f7404681b000b915
ms.sourcegitcommit: 8a7165b28ac6b40722186300c26002fb132e6e4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92749512"
---
# <a name="dcount_intersect-plugin"></a>dcount_intersect 外掛程式

根據 `hll` [2.. 16] ) 範圍中 (N 的值，計算 n 個集合之間的交集，並傳回 n 個 `dcount` 值。

給定的組<sub>1</sub>，s<sub>2</sub>，.。。 S<sub>n</sub> -傳回值會代表相異計數：  
S<sub>1</sub>、  
S<sub>1</sub> ∩ s<sub>2</sub>，  
S<sub>1</sub> ∩ s<sub>2</sub> ∩ s<sub>3</sub>，  
... ,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩ .。。∩ S<sub>n</sub>

```kusto
T | evaluate dcount_intersect(hll_1, hll_2, hll_3)
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `dcount_intersect(` *hll_1* 、 *hll_2* 、[ `,` *hll_3* `,` ...]`)`

## <a name="arguments"></a>引數

* *T* ：輸入表格式運算式。
* *hll_i* ： <sub>我</sub> 以函式計算的 set S 值 [`hll()`](./hll-aggfunction.md) 。

## <a name="returns"></a>傳回

傳回資料表 `dcount` ，其中每個資料行都有 N 個值 (，代表設定的交集) 。
資料行名稱為 s0、s1、... (直到 n-1) 為止。

## <a name="examples"></a>範例

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
