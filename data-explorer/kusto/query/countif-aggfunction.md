---
title: 'countif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 countif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/02/2020
ms.openlocfilehash: 9e81b4947f3a3a0b1102256cb7fd2f635ce4611b
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614965"
---
# <a name="countif-aggregation-function"></a>countif ( # A1 (彙總函式) 

傳回 Predicate** 評估為 `true` 的資料列計數。 只能用在 [摘要內匯總](summarizeoperator.md)的內容中。

## <a name="syntax"></a>語法

摘要 `countif(` *Predicate*述詞`)`

## <a name="arguments"></a>引數

述*詞：將*用於匯總計算的運算式。 述*詞可以是*傳回型別為 bool 的任何純量運算式 (評估為 true/false) 。

## <a name="returns"></a>傳回值

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

## <a name="see-also"></a>另請參閱

[count ( # B1 ](count-aggfunction.md) 函數，它會計算沒有述詞運算式的資料列。
