---
title: 'make_set_if ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) make_set_if。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 262676e2ee40b619c4984a23c23818c493aad47a
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103052"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if ( # A1 (彙總函式) 

傳回運算式在群組中使用的一組 `dynamic` 相異值的 (*Expr* JSON) 陣列，其述詞會*Predicate*評估為 `true` 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

`summarize``make_set_if(` *Expr*， *Predicate*述詞 [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* 述*詞：必須*評估為 `true` for *Expr*才能加入至結果的述詞。
* *MaxSize* 是選擇性的整數限制， (預設值為 *1048576*) 時傳回的元素數目上限。 MaxSize 值不能超過1048576。

## <a name="returns"></a>傳回

傳回運算式在群組中使用的一組 `dynamic` 相異值的 (*Expr* JSON) 陣列，其述詞會*Predicate*評估為 `true` 。
陣列的排序次序是未定義的。

> [!TIP]
> 若只要計算相異值，請使用 [dcountif ( # B1 ](dcountif-aggfunction.md)

## <a name="see-also"></a>另請參閱

[`make_set`](./makeset-aggfunction.md) 函數，它會執行相同的，但不含述詞運算式。

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
| summarize make_set_if(name, strlen(name) > 4)
```

|set_name|
|----|
|["George"，"Ringo"]|