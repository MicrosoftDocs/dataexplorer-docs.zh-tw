---
title: 方差() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的方差(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b1d2ea47060ecede855a3fb419bbbfe2bf0b538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504704"
---
# <a name="variance-aggregation-function"></a>方差 () (聚合函數)

計算整個群組的*Expr*的方差,會視為[樣本 。](https://en.wikipedia.org/wiki/Sample_%28statistics%29) 

* 已用公式![:alt 文字](./images/aggregations/variance-sample.png "方差樣本")

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`variance(` *Expr*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 

**傳回**

跨組的*Expr*的方差值。
 
**範例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[ 1, 2, 3, 4, 5]|2.5|