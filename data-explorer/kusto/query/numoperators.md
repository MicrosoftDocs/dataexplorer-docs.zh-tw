---
title: 數位運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的數位運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6667005960152814be952d93fe932b4971d5322b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512014"
---
# <a name="numerical-operators"></a>數值運算子

類型`int`,`long``real`和 表示數值類型。
以下運算子可在以下類型的對之間使用:

運算子       |描述                         |範例
---------------|------------------------------------|-----------------------
`+`            |加                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |減                            |`0.23 - 0.22`,
`*`            |乘                            |`1s * 5`, `2 * 2`
`/`            |除                              |`10m / 1s`, `4 / 2`
`%`            |模數                              |`4 % 2`
`<`            |小於                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |大於                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |Equals                              |`1 == 1`
`!=`           |不等於                          |`1 != 0`
`<=`           |小於或等於                       |`4 <= 5`
`>=`           |大於或等於                    |`5 >= 4`
`in`           |等於其中一個元素       |[見此處](inoperator.md)
`!in`          |不等於任何元素   |[見此處](inoperator.md)

**關於莫杜洛運算符的評論**

兩個數位的 modulo 始終在 Kusto 返回一個「小非負數」。
因此,兩個數位*N* % *D*的 modulo&le;是這樣的:0 *(N* % *D*) &lt; abs(D )。* *

例如,以下查詢:

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

產生此結果:

|加號  | 負加  | 加減  | 減號|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |