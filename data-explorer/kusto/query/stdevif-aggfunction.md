---
title: stdevif() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 stdevif()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4a64cf1bb69860a2a8bd64de91cb00c2f0ec296f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506965"
---
# <a name="stdevif-aggregation-function"></a>斯特迪夫() (聚合函數)

計算*謂詞*`true`計算到 的組中*Expr* [的 stdev。](stdev-aggfunction.md)

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`stdevif(` *Expr*`, `*謂詞*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 
* *謂詞*: 謂詞,如果為 true,*則 Expr*計算值將添加到標準差中。

**傳回**

*謂詞*計算`true`為 的組中*Expr*的標準偏差值。
 
**範例**

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|