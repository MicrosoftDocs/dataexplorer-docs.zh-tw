---
title: 'series_greater ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_greater。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 5e961e1dbcbb66ca13ee1d6fe1c04314778e07f5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242677"
---
# <a name="series_greater"></a>series_greater()

計算 `>` 兩個數值序列輸入的元素更高 () 邏輯運算。

## <a name="syntax"></a>語法

`series_greater (`*Series1* `,`*Series2*`)`

## <a name="arguments"></a>引數

* *Series1，Series2*：輸入數值陣列以進行元素的比較。 所有引數都必須是動態陣列。 

## <a name="returns"></a>傳回

布林值的動態陣列，包含兩個輸入之間的計算元素高階更高邏輯運算。 任何非數值元素或非現有的元素 (不同大小的陣列，) 產生 `null` 元素值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_s2 = series_greater(s1, s2)
```

|s1|s2|s1_greater_s2|
|---|---|---|
|[1，2，4]|[4，2，1]|[false，false，true]|

## <a name="see-also"></a>另請參閱

如需完整系列統計資料的比較，請參閱：
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
