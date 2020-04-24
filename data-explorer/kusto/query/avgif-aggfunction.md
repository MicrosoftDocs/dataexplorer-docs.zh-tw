---
title: avgif() (聚合函數) - Azure 數據資源管理器 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 vgif() 聚合函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61352be628b7c5a05085c092d0c022deaa0d9b6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518253"
---
# <a name="avgif-aggregation-function"></a>avgif () (聚合函數)

計算*的字*值`true`為的群組中*Expr*的[平均值](avg-aggfunction.md)。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`avgif(` *Expr*`, `*謂詞*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 值`null`的記錄將被忽略,並且不包括在計算中。
* *謂詞*: 謂詞,如果為 true,*則 Expr*計算的值將添加到平均值中。

**傳回**

*謂詞*計算`true`為 的組中*Expr*的平均值。
 
**範例**

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|