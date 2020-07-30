---
title: make_set_if （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 make_set_if （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8d6b26a13539d88aae57774cc35cf57d321b67f4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346893"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if （）（彙總函式）

傳回 `dynamic` *Expr*在群組中的一組相異值的（JSON）陣列，述詞會評估為*Predicate* `true` 。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

`summarize``make_set_if(` *Expr*、 *Predicate*述詞 [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* 述*詞：必須*評估為 for Expr 的述詞， `true` 才會加入至結果。 *Expr*
* *MaxSize*是傳回的最大專案數目的選擇性整數限制（預設為*1048576*）。 MaxSize 值不能超過1048576。

## <a name="returns"></a>傳回

傳回 `dynamic` *Expr*在群組中的一組相異值的（JSON）陣列，述詞會評估為*Predicate* `true` 。
陣列的排序次序未定義。

> [!TIP]
> 若只要計算相異值，請使用[dcountif （）](dcountif-aggfunction.md)

**另請參閱**

[`make_set`](./makeset-aggfunction.md)函式，它會執行相同的（不含述詞運算式）。

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