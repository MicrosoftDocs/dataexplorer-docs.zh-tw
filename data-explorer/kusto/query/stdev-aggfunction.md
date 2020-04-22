---
title: stdev() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 stdev()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d54af583db6f7aca0b436040c453249207a5ae59
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663104"
---
# <a name="stdev-aggregation-function"></a>stdev () (聚合函數)

計算整個群組中*Expr*的標準偏差,將群組視為[樣本](https://en.wikipedia.org/wiki/Sample_%28statistics%29)。 

* 使用的公式:

:::image type="content" source="images/aggregations/stdev-sample.png" alt-text="斯特夫樣本":::

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`stdev(` *Expr*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 

**傳回**

跨組*Expr*的標準偏差值。
 
**範例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.58113883008419|