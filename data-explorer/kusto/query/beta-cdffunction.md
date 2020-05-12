---
title: Beta_cdf （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Beta_cdf （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76b16098d9340a98fb3a456dfa947c089507da6c
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227684"
---
# <a name="beta_cdf"></a>beta_cdf()

傳回標準累計搶鮮版（Beta）分佈函數。

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

如果*probability*  =  `beta_cdf(` *x*,... `)` ，則機率 `beta_inv(` * *,... `)`  = *x*。

Beta 分佈常用於研究不同樣本 (例如人們在一天不同時段內花在看電視的時間) 之間的差異 (百分比)。

**語法**

`beta_cdf(`*x* `, `*Alpha* `, `搶鮮*版*`)`

**引數**

* *x*：要在其上評估函數的值。
* *Alpha*：分佈的參數。
* 搶鮮*版（Beta*）：散發的參數。

**傳回**

* 累計搶鮮[版（Beta）分佈函數](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function)。

**注意事項**

如果有任何引數是非數值，Beta_cdf （）會傳回 null 值。

如果 x < 0 或 x > 1，Beta_cdf （）會傳回 NaN 值。

如果 Alpha ≤0或 Beta ≤0，Beta_cdf （）會傳回 NaN 值。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.9, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_cdf(x, alpha, beta)
```

|x|alpha|beta|comment|b|
|---|---|---|---|---|
|0.9|10|20|有效的輸入|0.999999999999959|
|1.5|10|20|x > 1，產生 NaN|NaN|
|-10|10|20|x < 0，產生 NaN|NaN|
|0.1|-1|20|Alpha < 0，產生 NaN|NaN|


**另請參閱**


* 如需計算搶鮮版（Beta）累計機率密度函數的反函數，請參閱[Beta-inv （）](./beta-invfunction.md)。
* 如需電腦率密度函數，請參閱搶鮮[版（Beta）-pdf （）](./beta-pdffunction.md)。