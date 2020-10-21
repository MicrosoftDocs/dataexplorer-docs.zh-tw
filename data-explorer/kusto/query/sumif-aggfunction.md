---
title: 'sumif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 sumif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a62ff98810996aff1352b959768385bec76e8ba8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251334"
---
# <a name="sumif-aggregation-function"></a>sumif ( # A1 (彙總函式) 

傳回述*詞評估為的**運算式*總和 `true` 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

您也可以使用 [sum ( # B1 ](sum-aggfunction.md) 函數，這會加總沒有述詞運算式的資料列。

## <a name="syntax"></a>語法

摘要 `sumif(` *運算式* `,` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：用於匯總計算的運算式。 
* 述*詞：若*為 true，則會將*運算式*的計算值加入至總和。 

## <a name="returns"></a>傳回

述*詞評估為之* *Expr*的總和值 `true` 。

## <a name="example"></a>範例

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize sumif(day_of_birth, strlen(name) > 4)
```

|sumif_day_of_birth|
|----|
|32|