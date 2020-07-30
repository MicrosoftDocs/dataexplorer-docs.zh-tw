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
ms.openlocfilehash: 4222e0672290a6d934382dd6f922aec082a19af7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338672"
---
# <a name="variance-aggregation-function"></a>變異數（）（彙總函式）

計算每個群組中*Expr*的變異數，並考慮將群組當做[範例](https://en.wikipedia.org/wiki/Sample_%28statistics%29)。 

* 使用的公式：

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="差異樣本":::

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

總結 `variance(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中*Expr*的變異值。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1，2，3，4，5]|2.5|