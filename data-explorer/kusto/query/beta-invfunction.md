---
title: 'Beta_inv ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 Beta_inv。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: deb91e6131d5662017ebdf714a79d0ee391c8ba1
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103297"
---
# <a name="beta_inv"></a>beta_inv()

傳回搶鮮版（Beta）累計機率 Beta 密度函式的反向。

```kusto
beta_inv(0.1, 10.0, 50.0)
```

如果*機率*  =  `beta_cdf(` *x*,... `)` ，則機率 `beta_inv(` * *,... `)`  = *x*。 

Beta 分配可用於專案規劃中，以指定預期的完成時間和變化來建立可能完成時間模型。

## <a name="syntax"></a>語法

`beta_inv(`*probability*機率 `, `*Alpha* `, `*Beta 版*`)`

## <a name="arguments"></a>引數

* 機率 *：與*Beta 分佈相關聯的機率。
* *Alpha*：散發的參數。
* *Beta*：分佈的參數。

## <a name="returns"></a>傳回

* 搶鮮版（Beta）累計機率密度函數的反向 [Beta_cdf ( # B1 ](./beta-cdffunction.md)

**備註**

如果有任何引數不是數值，Beta_inv ( # A1 會傳回 null 值。

如果 Alpha ≤0或 Beta ≤0，Beta_inv ( # A1 會傳回 null 值。

如果 probability ≤0或 probability > 1，Beta_inv ( # A1 會傳回 NaN 值。

指定機率的值之後，Beta_inv ( # A1 會搜尋該值 x，使 Beta_cdf (x、Alpha、搶鮮版（Beta）) = probability。

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
|0.1|-1|20|Alpha 是 < 0，產生 NaN|NaN|

## <a name="see-also"></a>另請參閱

* 如需計算累計搶鮮版（Beta）散發功能，請參閱 [Beta-cdf ( # B1 ](./beta-cdffunction.md)。
* 如需電腦率 Beta 密度函式，請參閱 [Beta-pdf ( # B1 ](./beta-pdffunction.md)。
