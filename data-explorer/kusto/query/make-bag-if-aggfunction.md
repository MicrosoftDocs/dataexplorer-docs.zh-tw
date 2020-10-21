---
title: 'make_bag_if ( # A1 (彙總函式) -Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) make_bag_if。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 997ba519016bf6f6774af3f305ab78515c36c4ed
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249930"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if ( # A1 (彙總函式) 

傳回在 `dynamic` 群組中，述詞評估為之所有 *' Expr '* 值的 (JSON) 屬性包 (字典) *Predicate* `true` 。

> [!NOTE]
> 只能用在 [摘要內匯總](summarizeoperator.md)的內容中。

## <a name="syntax"></a>語法

`summarize``make_bag_if(` *Expr*， *Predicate*述詞 [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*： `dynamic` 將用於匯總計算的型別運算式。
* 述*詞：必須*評估為的述詞，才能 `true` 將 *' Expr '* 新增至結果。
* *MaxSize*：傳回的最大專案數的選擇性整數限制， (預設值為 *1048576*) 。 MaxSize 值不能超過1048576。

## <a name="returns"></a>傳回

傳回 `dynamic` (JSON) 屬性包 (字典) ，該群組中的所有 *' Expr '* 值都是屬性包 (字典) 的值，而述詞會*Predicate*評估為 `true` 。
將略過非字典值。
如果索引鍵出現在一個以上的資料列中，則會選取任意值，超出此索引鍵的可能值。

> [!NOTE]
> 函式類似于沒有述詞 [`make_bag`](./make-bag-aggfunction.md) 運算式的 make_bag_if ( # A1。

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

使用 [bag_unpack ( # B1 ](bag-unpackplugin.md) 外掛程式，將 make_bag_if ( # A3 輸出中的包金鑰轉換成資料行。 

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
