---
title: 'avgif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 avgif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e0d112554ff77cb9c591f787bbb62f9a92e3b19d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248027"
---
# <a name="avgif-aggregation-function"></a>avgif ( # A1 (彙總函式) 

計算述*詞評估為的整個*群組中的*Expr* [平均值](avg-aggfunction.md) `true` 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `avgif(` *運算式* `, ` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 具有值的記錄 `null` 會被忽略，且不會包含在計算中。
* 述*詞：若*為 true，則會將*Expr*計算值新增至平均值。

## <a name="returns"></a>傳回

*在群組中，* 述詞評估為的平均*Expr*值 `true` 。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|