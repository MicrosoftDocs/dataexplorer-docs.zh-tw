---
title: minif （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 minif （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91764aeb8c825a272c414df7a0572d3b8310e79f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346740"
---
# <a name="minif-aggregation-function"></a>minif （）（彙總函式）

傳回整個群組中述*詞評估為的最*小值 `true` 。

* 只能在[匯總](summarizeoperator.md)的內容中使用

請參閱- [min （）](min-aggfunction.md)函式，此函數會傳回不含述詞運算式之群組中的最小值。

## <a name="syntax"></a>語法

`summarize``minif(` *Expr* `,` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* 述*詞：如果*為 true，則會檢查*Expr*計算值的最小值。

## <a name="returns"></a>傳回

在述*詞評估為的整個*群組中， *Expr*的最小值 `true` 。

## <a name="examples"></a>範例

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|