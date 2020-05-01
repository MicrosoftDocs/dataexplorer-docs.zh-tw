---
title: 變異數（）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的變異數（）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f8afae2413d65618cf6b6e2f400df2500b06078
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618035"
---
# <a name="variance-aggregation-function"></a>變異數（）（彙總函式）

計算每個群組中*Expr*的變異數，並考慮將群組當做[範例](https://en.wikipedia.org/wiki/Sample_%28statistics%29)。 

* 使用的公式：

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="差異樣本":::

* 只能在[匯總](summarizeoperator.md)的內容中使用

**語法**

總結`variance(` *Expr*`)`

**引數**

* *Expr*：將用於匯總計算的運算式。 

**傳回**

整個群組中*Expr*的變異值。
 
**範例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1，2，3，4，5]|2.5|