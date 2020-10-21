---
title: 'maxif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 maxif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4df30f50d82e0ad5af87acaaa88b55f151a2a77a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251718"
---
# <a name="maxif-aggregation-function"></a>maxif ( # A1 (彙總函式) 

傳回整個群組的最大 *值，述詞會評估為* `true` 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

另請參閱- [max ( # B1 ](max-aggfunction.md) 函式，這會在沒有述詞運算式的群組中傳回最大值。

## <a name="syntax"></a>語法

`summarize``maxif(` *Expr* `,` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 
* 述*詞：若*為 true，則會檢查*Expr*計算值的最大值。

## <a name="returns"></a>傳回

整個群組*中，述詞評估的*最大*Expr*值 `true` 。

## <a name="examples"></a>範例

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|