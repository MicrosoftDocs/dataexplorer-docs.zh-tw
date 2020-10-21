---
title: 'stdev ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 stdev ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d2ed328930f01d3f9c52a675eab61805fd87593
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247075"
---
# <a name="stdev-aggregation-function"></a>stdev ( # A1 (彙總函式) 

在群組中計算 *Expr* 的標準差，並考慮將群組視為 [範例](https://en.wikipedia.org/wiki/Sample_%28statistics%29)。 

* 使用公式：

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="Stdev 範例":::

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `stdev(` *運算式*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中 *Expr* 的標準差值。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1，2，3，4，5]|1.58113883008419|