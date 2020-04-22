---
title: arg_min() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的arg_min(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 4531bd498cf9d9053d57d8b463c7b0adfcc9bf8d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663977"
---
# <a name="arg_min-aggregation-function"></a>arg_min() (聚合函數)

在組中尋找最小化*ExprTo 最小化*的行,並返回*ExprToReturn*的`*`值(或返回整個行)。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize`[`(`*名稱 exprto 最小化*`,`名稱`,`*exprto 傳回*】 ...`)=` `*`  |  `,` *ExprToReturn* *ExprToMinimize* [ `(` exprtoto 最小化 , exprto 傳回 】 `arg_min` ...`)`

**引數**

* *ExprTo 最小化*:將用於聚合計算的運算式。 
* *ExprToReturn*:當*ExprTo 最小化*為最小時,將用於返回該值的運算式。 要返回的運算式可以是通配符 (*), 用於返回輸入表的所有列。
* *名稱ExprTo最小化*:表示*ExprTo 最小化*的結果列的可選名稱。
* *NameExprToReturn:* 表示*ExprToReturn*的結果列的其他可選名稱。

**傳回**

在組中尋找最小化*ExprTo 最小化*的行,並返回*ExprToReturn*的`*`值(或返回整個行)。

**範例**

顯示每種產品最便宜的供應商:

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

顯示所有詳細資訊,而不僅僅是供應商名稱:

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

查找每個大陸最南端的城市,其國家:

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/aggregations/arg-min.png" alt-text="阿格敏":::
