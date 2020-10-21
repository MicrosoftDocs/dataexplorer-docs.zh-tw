---
title: '差異 ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) 的變異數。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a031efb5068e4497df0fa7acb54561c3b3caffb
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92240949"
---
# <a name="variance-aggregation-function"></a>差異 ( # A1 (彙總函式) 

在群組中計算 *Expr* 的變異數，並考慮將群組視為 [範例](https://en.wikipedia.org/wiki/Sample_%28statistics%29)。 

* 使用公式：

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="變異數範例":::

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `variance(` *運算式*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中 *Expr* 的變異數值。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1，2，3，4，5]|2.5|