---
title: 'series_less_equals ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_less_equals。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 0373fdf7c687dd7af63895ce3a542ae9b6b4f150
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103544"
---
# <a name="series_less_equals"></a>series_less_equals()

計算 `<=` 兩個數值序列輸入的 () 邏輯運算的較小或等於元素。

## <a name="syntax"></a>語法

`series_less_equals (`*Series1* `,`*Series2*`)`

## <a name="arguments"></a>引數

* *Series1，Series2*：輸入數值陣列以進行元素的比較。 所有引數都必須是動態陣列。 

## <a name="returns"></a>傳回

布林值的動態陣列，包含兩個輸入之間的計算元素較小或相等邏輯運算。 任何非數值元素或非現有的元素 (不同大小的陣列，) 產生 `null` 元素值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_equals_s2 = series_less_equals(s1, s2)
```

|s1|s2|s1_less_equals_s2|
|---|---|---|
|[1，2，4]|[4，2，1]|[true，true，false]|

## <a name="see-also"></a>另請參閱

如需完整系列統計資料的比較，請參閱：
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
