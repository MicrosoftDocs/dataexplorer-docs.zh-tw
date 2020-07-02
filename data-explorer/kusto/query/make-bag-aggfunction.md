---
title: make_bag （）（彙總函式）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 make_bag （）彙總函式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7c0d6ae10c21b1df55aaa3584f4f40e830b58d2c
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763438"
---
# <a name="make_bag-aggregation-function"></a>make_bag （）（彙總函式）

傳回 `dynamic` 群組中所有值的（JSON）屬性包（字典） *`Expr`* 。

* 只能在[匯總](summarizeoperator.md)的內容中使用

**語法**

`summarize``make_bag(` *`Expr`* [ `,` *MaxSize*]`)`

**引數**

* *Expr*：用於匯總計算之類型的運算式 `dynamic` 。
* *MaxSize*是傳回的元素數目上限的選擇性整數限制。 預設值為*1048576*。 MaxSize 值不能超過*1048576*。

**注意**

函式的舊版和過時變體 `make_dictionary()` 的預設限制為*MaxSize* = 128。

**傳回**

傳回 `dynamic` 群組中所有值的（JSON）屬性包（字典） *`Expr`* ，也就是屬性包。
將略過非字典值。
如果索引鍵出現在多個資料列中，則會選取任意值（超出此索引鍵的可能值）。

**另請參閱**

使用[bag_unpack （）](bag-unpackplugin.md)外掛程式，將動態 JSON 物件擴充至使用屬性包索引鍵的資料行。 

**範例**

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag(p)

```

|dict|
|----|
|{"prop01"： "val_a"，"prop02"： "val_b"，"prop03"： "val_c"} |

使用[bag_unpack （）](bag-unpackplugin.md)外掛程式，將 make_bag （）輸出中的包索引鍵轉換成資料行。 

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag(p)
| evaluate bag_unpack(bag) 

```

|prop01|prop02|prop03|
|---|---|---|
|val_a|val_b|val_c|
