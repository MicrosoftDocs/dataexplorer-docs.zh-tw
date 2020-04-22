---
title: stdevp() (聚合函數) - Azure 數據資源管理器 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的 stdevp()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0549c15ec9e2435d242f210e6dfc2163796e5f39
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663195"
---
# <a name="stdevp-aggregation-function"></a>stdevp() (聚合函數)

計算整個群組*Expr*的標準偏差,將群組視為[總體](https://en.wikipedia.org/wiki/Statistical_population)。 

* 使用的公式:

:::image type="content" source="images/aggregations/stdev-population.png" alt-text="斯特夫夫人口":::

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`stdevp(` *Expr*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 

**傳回**

跨組*Expr*的標準偏差值。
 
**範例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.4142135623731|