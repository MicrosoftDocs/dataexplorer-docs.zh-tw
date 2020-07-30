---
title: arg_min （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 arg_min （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 33e2657f2569957002d17d7061cfec863402027e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349681"
---
# <a name="arg_min-aggregation-function"></a>arg_min （）（彙總函式）

在群組中尋找最小化*ExprToMinimize*的資料列，並傳回*ExprToReturn*的值（或傳回 `*` 整個資料列）。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

`summarize`[ `(` *NameExprToMinimize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_min` `(`*ExprToMinimize*， `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>引數

* *ExprToMinimize*：將用於匯總計算的運算式。 
* *ExprToReturn*：將用來在*ExprToMinimize*最低時傳回值的運算式。 要傳回的運算式可能是萬用字元（*），以傳回輸入資料表的所有資料行。
* *NameExprToMinimize*：代表*ExprToMinimize*之結果資料行的選擇性名稱。
* *NameExprToReturn*：代表*ExprToReturn*之結果資料行的其他選擇性名稱。

## <a name="returns"></a>傳回

在群組中尋找最小化*ExprToMinimize*的資料列，並傳回*ExprToReturn*的值（或傳回 `*` 整個資料列）。

## <a name="examples"></a>範例

顯示每項產品的最低價供應商：

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

顯示所有詳細資料，而不只是供應商名稱：

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

尋找每個大陸中的 southernmost 城市，其國家/地區如下：

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/arg-min-aggfunction/arg-min.png" alt-text="Arg min":::
