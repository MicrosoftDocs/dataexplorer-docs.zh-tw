---
title: 'Beta_cdf ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 Beta_cdf。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8a3711594ec5d1cbcaf36c7286f1484a708c29a0
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90067516"
---
# <a name="beta_cdf"></a>beta_cdf()

傳回標準的累計 Beta 分佈函數。

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

如果*機率*  =  `beta_cdf(` *x*,... `)` ，則機率 `beta_inv(` * *,... `)`  = *x*。

Beta 分佈常用於研究不同樣本 (例如人們在一天不同時段內花在看電視的時間) 之間的差異 (百分比)。

## <a name="syntax"></a>語法

`beta_cdf(`*x* `, `*Alpha* `, `*Beta 版*`)`

## <a name="arguments"></a>引數

* *x*：要評估函數的值。
* *Alpha*：散發的參數。
* *Beta*：分佈的參數。

## <a name="returns"></a>傳回

* [累積 Beta 分佈函數](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function)。

**備註**

如果有任何引數不是數值，Beta_cdf ( # A1 會傳回 null 值。

如果 x < 0 或 x > 1，Beta_cdf ( # A2 會傳回 NaN 值。

如果 Alpha ≤0或 Alpha > 10000，Beta_cdf ( # A1 會傳回 NaN 值。

如果 Beta ≤0或 Beta > 10000，Beta_cdf ( # A1 會傳回 NaN 值。

## <a name="examples"></a>範例

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

|x|alpha|搶鮮版 (Beta)|comment|b|
|---|---|---|---|---|
|0.9|10|20|有效的輸入|0.999999999999959|
|1.5|10|20|x > 1，產生 NaN|NaN|
|-10|10|20|x < 0，產生 NaN|NaN|
|0.1|-1|20|Alpha 是 < 0，產生 NaN|NaN|


**另請參閱**


* 如需計算搶鮮版（Beta）累計機率密度函數的反向，請參閱 [Beta-inv ( # B1 ](./beta-invfunction.md)。
* 如需電腦率密度函式，請參閱 [Beta-pdf ( # B1 ](./beta-pdffunction.md)。
