---
title: make_bag_if() (聚合函數) - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的make_bag_if()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 201de637df387bb8925995a5e52c048255d1535c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512966"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if() (聚合函數)

傳回群組`dynamic`中*Expr*的所有值 (JSON) 屬性套件(字典),`true`*謂字*將計算到 。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize``make_bag_if(` *Expr*`,`,*無詞*[*最大尺寸*】`)`

**引數**

* *Expr*:`dynamic`用於聚合計算的類型運算式。
* *謂詞*:必須計算`true`到 的謂詞,才能將*Expr*添加到結果中。
* *MaxSize*是返回的最大元素數的可選整數限制(預設值為*1048576)。* 最大大小值不能超過 1048576。

**傳回**

傳回屬性套件 (JSON) 屬性套件(字典),該屬性包位於屬性套件(字典)組中*Expr*的所有值,謂*字*將評估到`true` `dynamic`
將跳過非字典值。
如果一個鍵出現在多個行中,將選擇任意值(從此鍵的可能值中)。

**另請參閱**

[`make_bag`](./make-bag-aggfunction.md)函數,它執行相同的,沒有謂詞表達式。

**範例**

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
|[ "prop01":"val_a","道具03":"val_c" | |

使用[bag_unpack()](bag-unpackplugin.md)外掛程式將make_bag_if()輸出中的包鑰匙轉換為列。 

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

|道具01|道具03|
|---|---|
|val_a|val_c|