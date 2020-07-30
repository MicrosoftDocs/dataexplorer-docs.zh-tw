---
title: Beta_pdf （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Beta_pdf （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1b94f661973d1ec89fe7f60edc9063b8c0f36d3c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349188"
---
# <a name="beta_pdf"></a>beta_pdf()

傳回機率密度搶鮮版（Beta）函數。

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

Beta 分佈常用於研究不同樣本 (例如人們在一天不同時段內花在看電視的時間) 之間的差異 (百分比)。

## <a name="syntax"></a>語法

`beta_pdf(`*x* `, `*Alpha* `, `搶鮮*版*`)`

## <a name="arguments"></a>引數

* *x*：要在其上評估函數的值。
* *Alpha*：分佈的參數。
* 搶鮮*版（Beta*）：散發的參數。

## <a name="returns"></a>傳回

* 機率搶鮮[版（Beta）密度函數](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function)。

**備註**

如果有任何引數是非數值，Beta_pdf （）會傳回 null 值。

如果 x ≤0或1≤ x，Beta_pdf （）會傳回 NaN 值。

如果 Alpha ≤0或 Beta ≤0，Beta_pdf （）會傳回 NaN 值。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.5, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend r = beta_pdf(x, alpha, beta)
```

|x|alpha|搶鮮版 (Beta)|comment|r|
|---|---|---|---|---|
|0.5|10|20|有效的輸入|0.746176019310951|
|1.5|10|20|x > 1，產生 NaN|NaN|
|-10|10|20|x < 0，產生 NaN|NaN|
|0.1|-1|20|Alpha < 0，產生 NaN|NaN|

**參考**

* 如需計算搶鮮版（Beta）累計機率密度函數的反函數，請參閱[Beta-inv （）](./beta-invfunction.md)。
* 如需標準累計 Beta 散發函式，請參閱搶鮮[版（Beta）-cdf （）](./beta-cdffunction.md)。
