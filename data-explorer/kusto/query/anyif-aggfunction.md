---
title: 'anyif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 anyif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d40b0a95b71999382f5fc4e83f108717ac5d683
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803381"
---
# <a name="anyif-aggregation-function"></a>anyif ( # A1 (彙總函式) 

任意為[摘要運算子](summarizeoperator.md)中的每個群組選取一筆記錄，其述詞為 "true"。 函式會針對每個這類記錄傳回運算式的值。

> [!NOTE]
> 當您想要針對複合群組索引鍵的每個值取得一個資料行的範例值時，此函數會很有用，但受限於某個屬於 "true" 的述詞。
> 如果有這類值，函數會嘗試傳回非 null/非空白的值。

## <a name="syntax"></a>語法

`summarize``anyif` `(` *Expr*、 *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：從要傳回的輸入中選取的每個記錄所傳回的運算式。
* 述*詞：指出*哪些記錄可視為進行評估的述詞。

## <a name="returns"></a>傳回

`anyif`彙總函式會針對從摘要運算子的每個群組中隨機選取的每個記錄，傳回計算出之運算式的值。 只能選取述詞會傳回 "true *" 的記錄*。 如果述詞未傳回 "true"，則會產生 null 值。

## <a name="examples"></a>範例

顯示填入300到600000000的隨機大陸。

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="任何1":::
