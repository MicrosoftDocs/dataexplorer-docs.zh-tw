---
title: beta_inv() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 azure 數據資源管理器中的beta_inv()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 20bdf8bfc01ef3ac6c6a12f6a43d87fd7b5c07e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517879"
---
# <a name="beta_inv"></a>beta_inv()

返回測試版累積概率測試密度函數的逆。

```kusto
beta_inv(0.1, 10.0, 50.0)
```

如果*概率* = `beta_cdf(`*x*,...`)`,然後`beta_inv(`*概率*,...x . *x* `)`  =  . 

Beta 分配可用於專案規劃中，以指定預期的完成時間和變化來建立可能完成時間模型。

**語法**

`beta_inv(`*概率*`, `*Alpha*`, `*貝塔*`)`

**引數**

* *概率*:與 Beta 分佈關聯的概率。
* *Alpha*: 分配的參數。
* *Beta*:分配的參數。

**傳回**

* 測試版累積概率密度函數[的逆beta_cdf()](./beta-cdffunction.md)

**注意事項**

如果任何參數是非數字參數,beta_inv() 傳回 null 值。

如果 Alpha = 0 或 beta = 0,beta_inv() 傳回空值。

如果概率 = 0 或概率> 1,beta_inv() 傳回 NaN 值。

給定概率值,beta_inv() 尋求該值 x,以便beta_cdf(x、Alpha、Beta) = 概率。

**範例**

```kusto
datatable(p:double, alpha:double, beta:double, comment:string)
[
    0.1, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "p > 1, yields null",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_inv(p, alpha, beta)
```

|p|alpha|beta|comment|b|
|---|---|---|---|---|
|0.1|10|20|有效輸入|0.226415022388749|
|1.5|10|20|p > 1,產生 null||
|0.1|-1|20|α 是 < 0,生成 NaN|NaN|

**另請參閱**

* 有關計算累積 Beta 分發函數,請參閱[beta-cdf()](./beta-cdffunction.md)。
* 有關計算概率測試密度函數,請參閱[beta-pdf()](./beta-pdffunction.md)。