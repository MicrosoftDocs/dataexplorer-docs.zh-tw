---
title: make_set_if() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的make_set_if()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1393e063fb0abb91b38a8b9e1edc0110e78b3638
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512643"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if() (聚合函數)

傳`dynamic`回*Expr*在群組中取得的一組不同值集 (JSON) 陣列,`true`*請您會*計算到 。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize``make_set_if(` *Expr*`,`,*無詞*[*最大尺寸*】`)`

**引數**

* *Expr*:將用於聚合計算的運算式。
* *謂詞*:必須`true`計算 到 的謂詞才能將*Expr*添加到結果中。
* *MaxSize*是返回的最大元素數的可選整數限制(預設值為*1048576)。* 最大大小值不能超過 1048576。

**傳回**

傳`dynamic`回*Expr*在群組中取得的一組不同值集 (JSON) 陣列,`true`*請您會*計算到 。
陣列的排序順序未定義。

> [!TIP]
> 要設定的不同的值,請使用[dcountif()](dcountif-aggfunction.md)

**另請參閱**

[`make_set`](./makeset-aggfunction.md)函數,它執行相同的,沒有謂詞表達式。

**範例**

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
|["喬治","林戈"*|