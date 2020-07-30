---
title: make_bag_if （）（彙總函式）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 make_bag_if （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 67e408653a4873dce3b5e8f21a91775573affbe2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347012"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if （）（彙總函式）

傳回 `dynamic` 群組中 *' Expr '* 所有值的（JSON）屬性包（字典），其述詞會評估為*Predicate* `true` 。

> [!NOTE]
> 只能用在[摘要內匯總](summarizeoperator.md)的內容中。

## <a name="syntax"></a>語法

`summarize``make_bag_if(` *Expr*、 *Predicate*述詞 [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*： `dynamic` 將用於匯總計算之型別的運算式。
* 述*詞：必須*評估為 `true` 的述詞，才能將 *' Expr '* 加入至結果。
* *MaxSize*：傳回的元素數目上限的選擇性整數限制（預設值為*1048576*）。 MaxSize 值不能超過1048576。

## <a name="returns"></a>傳回

`dynamic`針對述詞評估為*的*屬性包（字典），傳回群組中所有 *' Expr '* 值的（JSON）屬性包（字典） `true` 。
將略過非字典值。
如果索引鍵出現在多個資料列中，則會選取任意值（超出此索引鍵的可能值）。

> [!NOTE]
> 函式 [`make_bag`](./make-bag-aggfunction.md) 類似于不含述詞運算式的 make_bag_if （）。

## <a name="examples"></a>範例

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag_if(p, predicate)

```

|dict|
|----|
|{"prop01"： "val_a"，"prop03"： "val_c"} |

使用[bag_unpack （）](bag-unpackplugin.md)外掛程式，將 make_bag_if （）輸出中的包機碼轉換成資料行。 

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag_if(p, predicate)
| evaluate bag_unpack(bag)

```

|prop01|prop03|
|---|---|
|val_a|val_c|
