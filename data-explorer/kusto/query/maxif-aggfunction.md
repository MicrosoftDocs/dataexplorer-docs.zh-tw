---
title: maxif （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 maxif （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 471ca0e3d6623b77fd2d799949bfe060643798e2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346808"
---
# <a name="maxif-aggregation-function"></a>maxif （）（彙總函式）

傳回整個群組中述*詞評估為的最*大值 `true` 。

* 只能在[匯總](summarizeoperator.md)的內容中使用

另請參閱- [max （）](max-aggfunction.md)函數，它會傳回不含述詞運算式之群組的最大值。

## <a name="syntax"></a>語法

`summarize``maxif(` *Expr* `,` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 
* 述*詞：如果*為 true，則會檢查*Expr*計算值的上限。

## <a name="returns"></a>傳回

在述*詞評估為的整個*群組中， *Expr*的最大值 `true` 。

## <a name="examples"></a>範例

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|