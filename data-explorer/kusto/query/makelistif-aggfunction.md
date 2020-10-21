---
title: 'make_list_if ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) make_list_if。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 362fd1b64ba156115515efa3db3214b0f86c5ad7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241365"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if ( # A1 (彙總函式) 

傳回 `dynamic` 群組中所有*Expr*值的 (JSON) 陣列，其述詞會評估為*Predicate* `true` 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

`summarize``make_list_if(` *Expr*， *Predicate*述詞 [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* 述*詞：必須*評估為 `true` 的述詞，以便將*Expr*加入結果中。
* *MaxSize* 是選擇性的整數限制， (預設值為 *1048576*) 時傳回的元素數目上限。 MaxSize 值不能超過1048576。

## <a name="returns"></a>傳回

傳回 `dynamic` 群組中所有*Expr*值的 (JSON) 陣列，其述詞會評估為*Predicate* `true` 。
如果 `summarize` 未排序運算子的輸入，則產生之陣列中的專案順序是未定義的。
如果排序運算子的輸入 `summarize` ，則產生的陣列中的元素順序會追蹤輸入的專案順序。

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

## <a name="see-also"></a>另請參閱

[`make_list`](./makelist-aggfunction.md) 函數，它會執行相同的，但不含述詞運算式。