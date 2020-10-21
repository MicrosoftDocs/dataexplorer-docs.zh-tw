---
title: 'anyif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 anyif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 032541f34af7d8cbc07e0c02a854ba6b66657f8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248251"
---
# <a name="anyif-aggregation-function"></a>anyif ( # A1 (彙總函式) 

針對「 [摘要」運算子](summarizeoperator.md)中的每個群組（其述詞為 "true"），任意選取一筆記錄。 函數會針對每一筆記錄傳回運算式的值。

> [!NOTE]
> 當您想要針對複合群組索引鍵的每個值取得一個資料行的範例值時，此函式很有用，但受限於某些 "true" 的述詞。
> 如果有這類值，函數會嘗試傳回非 null/非空白值。

## <a name="syntax"></a>語法

`summarize``anyif` `(` *Expr*、 *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：從輸入中選取要傳回的每一筆記錄的運算式。
* 述*詞：指示*可考慮哪些記錄以進行評估。

## <a name="returns"></a>傳回

`anyif`彙總函式會針對從摘要運算子的每個群組隨機選取的每個記錄，傳回計算出的運算式值。 只可選取述詞傳回 "true *" 的記錄* 。 如果述詞未傳回 "true"，則會產生 null 值。

## <a name="examples"></a>範例

顯示人口為300至600000000的隨機大陸。

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="任何1":::
