---
title: any （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的任何（）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 73c3a660dc7a34f1f9fef840b13f47c13b4d1b2f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349732"
---
# <a name="any-aggregation-function"></a>any （）（彙總函式）

任意為[摘要運算子](summarizeoperator.md)中的每個群組選擇一筆記錄，並傳回一或多個運算式的值，而不是每一筆記錄。

## <a name="syntax"></a>語法

`summarize``any` `(` （*Expr* [ `,` *運算式 2* ...]） | `*``)`

## <a name="arguments"></a>引數

* *Expr*：從要傳回的輸入中選取的每個記錄所傳回的運算式。
* *運算式 2* .。 *ExprN*：其他運算式。

## <a name="returns"></a>傳回

`any`彙總函式會針對每個記錄（從摘要運算子的每個群組中隨機選取），傳回計算出之運算式的值。

如果有 `*` 提供引數，函式的行為就像是「摘要」運算子之輸入的所有資料行都將會禁止「分組依據」資料行（如果有的話）。

**備註**

當您想要取得每個複合群組索引鍵值的一個或多個資料行的範例值時，這個函數就很有用。

當使用單一資料行參考提供函數時，如果有這樣的值，它會嘗試傳回非 null/非空白值。

由於此函式的隨機本質，在運算子的單一應用程式中多次使用， `summarize` 並不等於使用多個運算式來單一時間。 前者可能會有每個應用程式選取不同的記錄，而後者則保證所有的值都是透過單一記錄（每個不同的群組）來計算。

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

:::image type="content" source="images/aggfunction/any2.png" alt-text="任何2":::

顯示每個隨機大陸的所有詳細資料：

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="任何3":::
