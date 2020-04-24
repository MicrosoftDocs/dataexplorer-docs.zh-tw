---
title: 方差(聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的方差(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9dfebb3796f07dec6c91d36d788a018f84f70961
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504670"
---
# <a name="varianceif-aggregation-function"></a>方差 () (聚合函數)

計算*的字*`true`值為的群組中*Expr* [的方差](variance-aggfunction.md)。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`varianceif(` *Expr*`, `*謂詞*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 
* *謂詞*: 謂詞,如果為 true,*則 Expr*計算值將添加到方差中。

**傳回**

*謂詞*計算`true`為 的組中*Expr*的方差值。
 
**範例**

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|