---
title: 'series_not_equals ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_not_equals。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: c55cd4c4846f568dccc7f17e11e024f97c87f14b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246047"
---
# <a name="series_not_equals"></a>series_not_equals()

計算 `!=` 兩個數值序列輸入的 () 邏輯運算的不等於元素。

## <a name="syntax"></a>語法

`series_not_equals (`*Series1* `,`*Series2*`)`

## <a name="arguments"></a>引數

* *Series1，Series2*：輸入數值陣列以進行元素的比較。 所有引數都必須是動態陣列。 

## <a name="returns"></a>傳回

布林值的動態陣列，包含兩個輸入之間的計算專案不相等邏輯運算。 任何非數值元素或非現有的元素 (不同大小的陣列，) 產生 `null` 元素值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_not_equals_s2 = series_not_equals(s1, s2)
```

|s1|s2|s1_not_equals_s2|
|---|---|---|
|[1，2，4]|[4，2，1]|[true，false，true]|

## <a name="see-also"></a>另請參閱

如需完整系列統計資料的比較，請參閱：
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
