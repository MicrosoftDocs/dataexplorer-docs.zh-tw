---
title: '任何 ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中任何 ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4d718cfb46e3a404c943d579feaf4733499ab3e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248225"
---
# <a name="any-aggregation-function"></a>任何 ( # A1 (彙總函式) 

在 [摘要運算子](summarizeoperator.md)中任意選擇每個群組的一筆記錄，並傳回一或多個運算式在這類記錄上的值。

## <a name="syntax"></a>語法

`summarize``any` `(` (*運算式*[ `,` *運算式 2* ..]) |`*``)`

## <a name="arguments"></a>引數

* *Expr*：從輸入中選取要傳回的每一筆記錄的運算式。
* *運算式 2* .。 *ExprN*：其他運算式。

## <a name="returns"></a>傳回

`any`彙總函式會傳回針對每個記錄所計算的運算式值（從摘要運算子的每個群組隨機選取）。

如果 `*` 有提供引數，則函式的行為會如同「摘要」運算子之輸入的所有資料行都有群組依據資料行（如果有的話）。

**備註**

當您想要針對複合群組索引鍵的每個值取得一或多個資料行的範例值時，這個函數很有用。

當函數是以單一資料行參考提供時，如果有這類值，它就會嘗試傳回非 null/非空白值。

由於此函式的隨機本質，在運算子的單一應用程式中多次使用它， `summarize` 並不等於使用具有多個運算式的單一時間。 前者可能會讓每個應用程式選取不同的記錄，而後者保證所有值都是針對每個不同群組)  (單一記錄來計算。

## <a name="examples"></a>範例

顯示隨機大陸：

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="任何1":::

顯示隨機記錄的所有詳細資料：

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggfunction/any2.png" alt-text="任何1":::

顯示每個隨機大陸的所有詳細資料：

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="任何1":::
