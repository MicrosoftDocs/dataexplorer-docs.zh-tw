---
title: 任何() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的任何()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2a0b2aed48c9c5aa9d5b99bdb6cab68375827d2c
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030190"
---
# <a name="any-aggregation-function"></a>任何() (聚合函數)

任意選擇[彙總運算符](summarizeoperator.md)中每個組一個記錄,並返回一個或多個表達式的值。每個這樣的記錄。

**語法**

`summarize``any` *Expr* (Expr = Expr2 ...)| `(` `,` *Expr2*`*``)`

**引數**

* *Expr*: 從輸入中選擇要返回的每個記錄上的運算式。
* *Expr2* . . . *ExprN*:其他運算式。

**傳回**

聚合`any`函數返回為每個記錄計算的表達式的值,這些運算式是從匯總運算符的每個組中隨機選擇的。

如果提供`*`參數,則函數的作用就好像表達式都是對匯總運算符的輸入的列,禁止分組列(如果有)。

**備註**

如果要獲取複合組鍵每個值的一個或多個列的示例值,則此功能非常有用。

當函數提供單個列引用時,它將嘗試返回非空/非空值(如果存在)。

由於此函數的隨機性,在`summarize`運算符的單個應用程式中多次使用它並不等於使用具有多個表達式的單個時間。 前者可能讓每個應用程式選擇不同的記錄,而後者保證所有值都通過單個記錄(每個不同的組)計算。

**範例**

顯示隨機大陸:

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="任何 1":::

顯示隨機記錄的所有詳細資訊:

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggfunction/any2.png" alt-text="任 2":::

顯示每個隨機大陸的所有詳細資訊:

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="任何 3":::
