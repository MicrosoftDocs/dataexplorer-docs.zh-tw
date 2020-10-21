---
title: 'varianceif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 varianceif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 71c80afa5a2cfe79fb17aeb610f62c3076dd5f84
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245597"
---
# <a name="varianceif-aggregation-function"></a>varianceif ( # A1 (彙總函式) 

在述*詞評估為的群組*中計算*Expr* [的變異](variance-aggfunction.md)數 `true` 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `varianceif(` *運算式* `, ` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 
* 述*詞：若*為 true，則會將*Expr*計算值加入至變異數。

## <a name="returns"></a>傳回

在述*詞評估為*的群組中， *Expr*的變異值 `true` 。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|