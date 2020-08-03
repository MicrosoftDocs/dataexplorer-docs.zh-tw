---
title: countif （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 countif （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/02/2020
ms.openlocfilehash: 669978d8828f54926a8535f199ef7a9bc2ba7451
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515765"
---
# <a name="countif-aggregation-function"></a>countif （）（彙總函式）

傳回 Predicate** 評估為 `true` 的資料列計數。

* 只能在[匯總](summarizeoperator.md)的內容中使用

另請參閱- [count （）](count-aggfunction.md)函數，這會計算不含述詞運算式的資料列。

## <a name="syntax"></a>Syntax

摘要 `countif(` *Predicate*述詞`)`

## <a name="arguments"></a>引數

述*詞：將*用於匯總計算的運算式。 述*詞可以是*傳回類型為 bool 的任何純量運算式（評估為 true/false）。

## <a name="returns"></a>傳回

傳回 Predicate** 評估為 `true` 的資料列計數。

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
| summarize countif(strlen(name) > 4)
```

|countif_|
|----|
|2|

