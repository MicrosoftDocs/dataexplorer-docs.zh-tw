---
title: make_list_if （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 make_list_if （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dda177c39959f860ad7e019371133f16e1de91e2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346927"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if （）（彙總函式）

傳回 `dynamic` 群組中*Expr*所有值的（JSON）陣列，其中*的述*詞會評估為 `true` 。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

`summarize``make_list_if(` *Expr*、 *Predicate*述詞 [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* 述*詞：必須*評估為 `true` ，才能將*Expr*加入至結果。
* *MaxSize*是傳回的最大專案數目的選擇性整數限制（預設為*1048576*）。 MaxSize 值不能超過1048576。

## <a name="returns"></a>傳回

傳回 `dynamic` 群組中*Expr*所有值的（JSON）陣列，其中*的述*詞會評估為 `true` 。
如果 `summarize` 未排序運算子的輸入，則產生之陣列中的專案順序會是未定義的。
如果對運算子的輸入 `summarize` 進行排序，則產生之陣列中的專案順序會追蹤輸入的內容。

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
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|["George"，"Ringo"]|

**另請參閱**

[`make_list`](./makelist-aggfunction.md)函式，它會執行相同的（不含述詞運算式）。