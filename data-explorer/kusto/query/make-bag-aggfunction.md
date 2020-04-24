---
title: make_bag() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的make_bag()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16f5f5663c4807a766d99c12020ff0a46c4db336
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512983"
---
# <a name="make_bag-aggregation-function"></a>make_bag() (聚合函數)

返回組中`dynamic` *Expr*的所有值的 (JSON) 屬性包(字典)。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize``make_bag(` *Expr* `,` [*最大尺寸*】`)`

**引數**

* *Expr*:`dynamic`用於聚合計算的類型運算式。
* *MaxSize*是返回的最大元素數的可選整數限制(預設值為*1048576)。* 最大大小值不能超過 1048576。

**注意**

此函數的舊版和過時變體:`make_dictionary()`預設限制為*MaxSize* = 128。

**傳回**

返回組`dynamic`中*Expr*的所有值 (字典) 的 (JSON) 屬性包(字典)。
將跳過非字典值。
如果一個鍵出現在多個行中,將選擇任意值(從此鍵的可能值中)。

**另請參閱**

使用[bag_unpack()外掛程式](bag-unpackplugin.md)使用屬性包鍵將動態 JSON 物件擴展到列中。 

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
|[ "道具01":"val_a","道具02":"val_b","道具03":"val_c" | |

使用[bag_unpack()外掛程式](bag-unpackplugin.md)將make_bag()輸出中的包鑰匙轉換為列。 

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

|道具01|道具02|道具03|
|---|---|---|
|val_a|val_b|val_c|