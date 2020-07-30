---
title: sumif （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 sumif （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cd9900b5087ed0d6ae7e97d2f2dad809bb909331
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350803"
---
# <a name="sumif-aggregation-function"></a>sumif （）（彙總函式）

傳回述*詞評估為的* *Expr*總和 `true` 。

* 只能在[匯總](summarizeoperator.md)的內容中使用

您也可以使用[sum （）](sum-aggfunction.md)函數來計算不含述詞運算式的資料列。

## <a name="syntax"></a>語法

總結 `sumif(` *Expr* `,` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：匯總計算的運算式。 
* 述*詞：述*詞，如果為 true，則會將*Expr*的計算值新增至總和。 

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