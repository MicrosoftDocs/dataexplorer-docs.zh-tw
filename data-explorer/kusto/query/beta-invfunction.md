---
title: Beta_inv （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Beta_inv （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b69fed2b3d7028fdc29d8098e8358c0088fcd8bb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349205"
---
# <a name="beta_inv"></a>beta_inv()

傳回搶鮮版（Beta）累計機率測試密度函數的反向。

```kusto
beta_inv(0.1, 10.0, 50.0)
```

如果*probability*  =  `beta_cdf(` *x*,... `)` ，則機率 `beta_inv(` * *,... `)`  = *x*。 

Beta 分配可用於專案規劃中，以指定預期的完成時間和變化來建立可能完成時間模型。

## <a name="syntax"></a>語法

`beta_inv(`*probability*機率 `, `*Alpha* `, `搶鮮*版*`)`

## <a name="arguments"></a>引數

* 機率：與搶鮮*版（Beta*）分佈相關聯的機率。
* *Alpha*：分佈的參數。
* 搶鮮*版（Beta*）：散發的參數。

## <a name="returns"></a>傳回

* 搶鮮版（Beta）累計機率密度函數[Beta_cdf （）](./beta-cdffunction.md)的反向

**備註**

如果有任何引數是非數值，Beta_inv （）會傳回 null 值。

如果 Alpha ≤0或 Beta ≤0，Beta_inv （）會傳回 null 值。

如果 probability ≤0或 probability > 1，Beta_inv （）會傳回 NaN 值。

假設有 [機率] 的值，Beta_inv （）會搜尋值 x，使 Beta_cdf （x，Alpha，Beta） = 機率。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(p:double, alpha:double, beta:double, comment:string)
[
    0.1, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "p > 1, yields null",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_inv(p, alpha, beta)
```

|p|alpha|搶鮮版 (Beta)|comment|b|
|---|---|---|---|---|
|0.1|10|20|有效的輸入|0.226415022388749|
|1.5|10|20|p > 1，產生 null||
|0.1|-1|20|Alpha < 0，產生 NaN|NaN|

**另請參閱**

* 如需計算累計 Beta 散發函式，請參閱搶鮮[版（Beta）-cdf （）](./beta-cdffunction.md)。
* 如需電腦率測試密度功能，請參閱[Beta-pdf （）](./beta-pdffunction.md)。
