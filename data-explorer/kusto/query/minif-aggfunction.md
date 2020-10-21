---
title: 'minif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 minif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f6932a50d59ee3df73857bfd4230faaa2e10dd2b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251013"
---
# <a name="minif-aggregation-function"></a>minif ( # A1 (彙總函式) 

傳回整個群組 *中，述詞評估為* 的最小值 `true` 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

請參閱- [min ( # B1 ](min-aggfunction.md) 函式，此函式會在沒有述詞運算式的群組中傳回最小值。

## <a name="syntax"></a>語法

`summarize``minif(` *Expr* `,` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* 述*詞：若*為 true，則會檢查*Expr*計算值的最小值。

## <a name="returns"></a>傳回

整個群組*中，述詞評估為*的*運算式*最小值 `true` 。

## <a name="examples"></a>範例

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|