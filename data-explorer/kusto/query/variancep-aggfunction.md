---
title: 'variancep ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 variancep ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6cb9ce6ab41e5417d9d39d90d6b7fb31dd8b1fab
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251310"
---
# <a name="variancep-aggregation-function"></a>variancep ( # A1 (彙總函式) 

在群組中計算 *Expr* 的變異數，並考慮將群組視為 [人口](https://en.wikipedia.org/wiki/Statistical_population)。 

* 使用公式：

:::image type="content" source="images/variancep-aggfunction/variance-population.png" alt-text="差異擴展":::

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `variancep(` *運算式*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中 *Expr* 的變異數值。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[1，2，3，4，5]|2|