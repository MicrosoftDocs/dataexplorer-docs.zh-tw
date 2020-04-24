---
title: make_set() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的make_set()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: db11bd528703323d54ff96b228c0b80bbcb86dd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512677"
---
# <a name="make_set-aggregation-function"></a>make_set() (聚合函數)

傳回一組相異值的 `dynamic` (JSON) 陣列，這些是 Expr** 在群組中取得的值。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize``make_set(` *Expr* `,` [*最大尺寸*】`)`

**引數**

* *Expr*:用於聚合計算的運算式。
* *MaxSize*是返回的最大元素數的可選整數限制(預設值為*1048576)。* 最大大小值不能超過 1048576。

> [!NOTE]
> 此函數的舊版和過時變體:`makeset()`預設限制為*MaxSize* = 128。

**傳回**

傳回一組相異值的 `dynamic` (JSON) 陣列，這些是 Expr** 在群組中取得的值。
陣列的排序順序未定義。

> [!TIP]
> 要設定的不同的值,請使用[dcount()](dcount-aggfunction.md)

**範例**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

![alt 文字](./images/aggregations/makeset.png "makeset")

**另請參閱**

* 對[`mv-expand`](./mvexpandoperator.md)相反的函數使用運算符。
* [`make_set_if`](./makesetif-aggfunction.md)運算符與`make_set`類似,但它還接受謂詞。