---
title: 'make_bag ( # A1 (彙總函式) -Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 彙總函式的 make_bag。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3258a847a526e0e3b6ac8f0186b0a1aaabc3ffe5
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103201"
---
# <a name="make_bag-aggregation-function"></a>make_bag ( # A1 (彙總函式) 

傳回 `dynamic` 群組中所有值的 (JSON) 屬性包 (字典) *`Expr`* 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

`summarize``make_bag(` *`Expr`* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*：用於匯總計算的型別運算式 `dynamic` 。
* *MaxSize* 是傳回的元素數目上限的選擇性整數限制。 預設值為 *1048576*。 MaxSize 值不能超過 *1048576*。

**注意**

函式的舊版和過時變數的 `make_dictionary()` 預設限制為 *MaxSize* = 128。

## <a name="returns"></a>傳回

傳回 `dynamic` 群組中所有值的 (JSON) 屬性包 (字典) *`Expr`* ，也就是屬性包。
將略過非字典值。
如果索引鍵出現在一個以上的資料列中，則會選取任意值，超出此索引鍵的可能值。

## <a name="see-also"></a>另請參閱

使用 [bag_unpack ( # B1 ](bag-unpackplugin.md) 外掛程式將動態 JSON 物件擴充至使用屬性包索引鍵的資料行。 

## <a name="examples"></a>範例

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

使用 [bag_unpack ( # B1 ](bag-unpackplugin.md) 外掛程式，將 make_bag ( # A3 輸出中的包金鑰轉換成資料行。 

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
