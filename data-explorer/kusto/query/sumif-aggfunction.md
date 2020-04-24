---
title: sumif() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 sumif()聚合函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7d2c96f73b404e8d9acbe9da9defecd6bf1bbf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506659"
---
# <a name="sumif-aggregation-function"></a>sumif() (聚合函數)

返回*謂詞*`true`計算到 的*Expr*的總和。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

您還可以使用[sum()](sum-aggfunction.md)函數,該函數在沒有謂詞表達式的情況下對行求和。

**語法**

總結`sumif(` *Expr*`,`*謂詞*`)`

**引數**

* *Expr*:用於聚合計算的運算式。 
* *謂詞*: 謂詞, 如果為*true,Expr*的計算值將添加到總和中。 

**傳回**

*謂詞*計算`true`到 的*Expr*的總和值。

> [!TIP]
> 使用 `summarize sumif(expr, filter)` 來取代 `where filter | summarize sum(expr)`

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
| summarize sumif(day_of_birth, strlen(name) > 4)
```

|sumif_day_of_birth|
|----|
|32|