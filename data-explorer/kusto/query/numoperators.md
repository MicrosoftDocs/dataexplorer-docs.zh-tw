---
title: 數值運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的數值運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e020d5f6692c8c290b6a68b2ed1b52bc710c89c8
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94713984"
---
# <a name="numerical-operators"></a>數值運算子

類型 `int` 、 `long` 和 `real` 代表數數值型別。
下列運算子可用於這兩種類型的配對：

運算子       |描述                         |範例
---------------|------------------------------------|-----------------------
`+`            |新增                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |減去                            |`0.23 - 0.22`,
`*`            |乘以                            |`1s * 5`, `2 * 2`
`/`            |除以                              |`10m / 1s`, `4 / 2`
`%`            |模數                              |`4 % 2`
`<`            |小於                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |大於                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |等於                              |`1 == 1`
`!=`           |不等於                          |`1 != 0`
`<=`           |小於或等於                       |`4 <= 5`
`>=`           |大於或等於                    |`5 >= 4`
`in`           |等於其中一個元素       |[請參閱這裡](inoperator.md)
`!in`          |不等於任何元素   |[請參閱這裡](inoperator.md)

> [!NOTE]
> 若要從一個數數值型別轉換成另一個，請使用 `to*()` 函數。 例如，請參閱 [`tolong()`](tolongfunction.md) 和 [`toint()`](tointfunction.md) 。

**模數運算子的相關批註**

兩個數字的模數一律會在 Kusto 中傳回「小非負數」。
因此，兩個數字的模數（ *n*  %  *d*）如下： 0 &le; (*n*  %  *d*) &lt; abs (*D*) 。

例如，下列查詢：

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

產生下列結果：

|plusPlus  | minusPlus  | plusMinus  | minusMinus|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |
