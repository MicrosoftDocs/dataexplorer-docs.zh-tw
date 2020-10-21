---
title: 'arg_min ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) arg_min。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 5c3e7912839dd3258c4a3f96530fbf438bf4ef4f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245606"
---
# <a name="arg_min-aggregation-function"></a>arg_min ( # A1 (彙總函式) 

在群組中尋找最小化 *ExprToMinimize*的資料列，並傳回 *ExprToReturn* (的值，或傳回 `*` 整個資料列) 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

`summarize`[ `(` *NameExprToMinimize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_min` `(`*ExprToMinimize*， `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>引數

* *ExprToMinimize*：將用於匯總計算的運算式。 
* *ExprToReturn*：當 *ExprToMinimize* 為最小值時，將用來傳回值的運算式。 要傳回的運算式可以是萬用字元 ( * ) 傳回輸入資料表的所有資料行。
* *NameExprToMinimize*：代表 *ExprToMinimize*之結果資料行的選擇性名稱。
* *NameExprToReturn*：代表 *ExprToReturn*之結果資料行的其他選擇性名稱。

## <a name="returns"></a>傳回

在群組中尋找最小化 *ExprToMinimize*的資料列，並傳回 *ExprToReturn* (的值，或傳回 `*` 整個資料列) 。

## <a name="examples"></a>範例

顯示每項產品的最低價供應商：

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

顯示所有詳細資料，而不只是供應商名稱：

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

在每個大陸中尋找 southernmost 城市，其國家/地區為：

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/arg-min-aggfunction/arg-min.png" alt-text="Arg min":::
