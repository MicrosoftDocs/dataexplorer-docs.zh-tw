---
title: beta_pdf() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 beta_pdf()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53b86d88b05cc6c5cc31f1e3bbb9e3e712eed7f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517862"
---
# <a name="beta_pdf"></a>beta_pdf()

返回概率密度測試函數。

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

Beta 分佈常用於研究不同樣本 (例如人們在一天不同時段內花在看電視的時間) 之間的差異 (百分比)。

**語法**

`beta_pdf(`*x*`, `*阿爾法*`, `*測試版*`)`

**引數**

* *x*: 用於計算函數的值。
* *Alpha*: 分配的參數。
* *Beta*:分配的參數。

**傳回**

* [概率測試點密度函數](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function)。

**注意事項**

如果任何參數是非數字參數,beta_pdf() 傳回 null 值。

如果 x = 0 或 1 = x,beta_pdf() 傳回 NaN 值。

如果 Alpha = 0 或 beta = 0,beta_pdf() 傳回 NaN 值。

**範例**

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

|x|alpha|beta|comment|r|
|---|---|---|---|---|
|0.5|10|20|有效輸入|0.746176019310951|
|1.5|10|20|x > 1, 產生 NaN|NaN|
|-10|10|20|x < 0,生成 NaN|NaN|
|0.1|-1|20|α 是 < 0,生成 NaN|NaN|

**參考**

* 有關計算 Beta 累積概率密度函數的逆,請參閱[beta inv()](./beta-invfunction.md)。
* 有關標準累積 Beta 分發功能,請參閱[beta-cdf()](./beta-cdffunction.md)。