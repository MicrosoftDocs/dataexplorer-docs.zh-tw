---
title: 'stdevp ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 stdevp ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16ae0e297dacefb3a9cc8bc7efb579393d89c968
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243834"
---
# <a name="stdevp-aggregation-function"></a>stdevp ( # A1 (彙總函式) 

在群組中計算 *Expr* 的標準差，並考慮將群組視為 [人口](https://en.wikipedia.org/wiki/Statistical_population)。 

* 使用公式：

:::image type="content" source="images/stdevp-aggfunction/stdev-population.png" alt-text="Stdev 人口":::

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `stdevp(` *運算式*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中 *Expr* 的標準差值。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[1，2，3，4，5]|1.4142135623731|