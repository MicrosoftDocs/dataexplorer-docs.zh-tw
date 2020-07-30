---
title: avgif （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 avgif （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 587af53de774332db70ef9bffcadf74d9e2c069d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349375"
---
# <a name="avgif-aggregation-function"></a>avgif （）（彙總函式）

計算述*詞評估為的整個*群組中的*Expr* [平均值](avg-aggfunction.md) `true` 。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

總結 `avgif(` *Expr* `, ` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 具有值的記錄 `null` 會被忽略，而且不會包含在計算中。
* 述*詞：如果*為 true，則會將*Expr*計算值加到平均值。

## <a name="returns"></a>傳回

在述*詞評估為*的整個群組中， *Expr*的平均值 `true` 。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|