---
title: stdev （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 stdev （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3a29621a18a364145585022b1f0651100cadab1c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618527"
---
# <a name="stdev-aggregation-function"></a>stdev （）（彙總函式）

在群組中計算*Expr*的標準差，並考慮將群組當做[範例](https://en.wikipedia.org/wiki/Sample_%28statistics%29)。 

* 使用的公式：

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="Stdev 範例":::

* 只能在[匯總](summarizeoperator.md)的內容中使用

**語法**

總結`stdev(` *Expr*`)`

**引數**

* *Expr*：將用於匯總計算的運算式。 

**傳回**

整個群組的*Expr*標準差值。
 
**範例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1，2，3，4，5]|1.58113883008419|