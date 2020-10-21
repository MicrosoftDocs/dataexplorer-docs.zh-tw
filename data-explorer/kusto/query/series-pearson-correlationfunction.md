---
title: 'series_pearson_correlation ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_pearson_correlation。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: aa75e3cb2f2aefbc50c148486cb687841408e1d4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245963"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

計算兩個數值序列輸入的皮耳森相互關聯係數。

請參閱： [皮耳森相互關聯係數](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)。

## <a name="syntax"></a>語法

`series_pearson_correlation(`*Series1* `,`*Series2*`)`

## <a name="arguments"></a>引數

* *Series1，Series2*：輸入數值陣列來計算相互關聯係數。 所有引數都必須是相同長度的動態陣列。 

## <a name="returns"></a>傳回

這兩個輸入之間的計算皮耳森相互關聯係數。 任何非數值元素或非現有的元素 (不同大小的陣列，) 產生 `null` 結果。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1，2，3，4，5]|[2，4，6，8，10]|1|
