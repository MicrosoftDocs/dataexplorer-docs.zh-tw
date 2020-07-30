---
title: variancep （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 variancep （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 70118746a079d76b1b6729bed3aae96c48399538
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338655"
---
# <a name="variancep-aggregation-function"></a>variancep （）（彙總函式）

計算每個群組中*Expr*的變異數，並[考慮將群組視為擴展。](https://en.wikipedia.org/wiki/Statistical_population) 

* 使用的公式：

:::image type="content" source="images/variancep-aggfunction/variance-population.png" alt-text="差異擴展":::

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

總結 `variancep(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中*Expr*的變異值。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[1，2，3，4，5]|2|