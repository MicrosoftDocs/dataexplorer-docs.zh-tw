---
title: make_list_if() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的make_list_if()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b34c1dad7be709145c622c97b357734c25292bba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512728"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if () (聚合函數)

傳回群組`dynamic`中*Expr*的所有值 (JSON) 陣列,`true`*謂字*會計算到 。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize``make_list_if(` *Expr*`,`,*無詞*[*最大尺寸*】`)`

**引數**

* *Expr*:將用於聚合計算的運算式。
* *謂詞*:必須計算`true`到 的謂詞,才能將*Expr*添加到結果中。
* *MaxSize*是返回的最大元素數的可選整數限制(預設值為*1048576)。* 最大大小值不能超過 1048576。

**傳回**

傳回群組`dynamic`中*Expr*的所有值 (JSON) 陣列,`true`*謂字*會計算到 。
如果未對`summarize`運算符的輸入進行排序,則生成的陣列中的元素順序將未定義。
如果對`summarize`運算符的輸入進行排序,則生成的陣列中的元素順序將追蹤輸入的順序。

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
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|["喬治","林戈"*|

**另請參閱**

[`make_list`](./makelist-aggfunction.md)函數,它執行相同的,沒有謂詞表達式。