---
title: 'stdevif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 stdevif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 831011bcd72c2fc9b06c3c68c6ebda7929d49e4b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242537"
---
# <a name="stdevif-aggregation-function"></a>stdevif ( # A1 (彙總函式) 

在述*詞評估為的群組*中，計算*Expr*的[stdev](stdev-aggfunction.md) `true` 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `stdevif(` *運算式* `, ` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 
* 述*詞：若*為 true，則會將*Expr*計算值新增至標準差。

## <a name="returns"></a>傳回

在*群組中，* 述詞評估為的*運算式*標準差值 `true` 。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|