---
title: 'series_outliers ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_outliers。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: b3ee62940e70f08fa7afb3abf011ee02bda02c7a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246014"
---
# <a name="series_outliers"></a>series_outliers()

針對數列中的異常點進行評分。

此函式會採用包含動態數值陣列的運算式做為輸入，並產生相同長度的動態數值陣列。 陣列的每個值都會使用「 [Tukey 的測試](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)」來指出可能異常的分數。 在輸入的相同元素中，大於1.5 的值表示引發或拒絕異常狀況。 小於-1.5 的值表示拒絕異常。

## <a name="syntax"></a>語法

`series_outliers(`*x* `, `*種類* `, `*ignore_val* `, `*min_percentile* `, `*max_percentile*`)`

## <a name="arguments"></a>引數

* *x*：動態陣列資料格，這是數值的陣列
* *種類：極端*偵測的演算法。 目前支援 `"tukey"` (傳統的「Tukey」 )  `"ctukey"` 和 (自訂「Tukey」 ) 。 預設為 `"ctukey"`
* *ignore_val*：指出數列中遺漏值的數值。 預設值為 double (null) 。 Null 和 ignore 值的分數設定為 `0`
* *min_percentile*：用於計算標準分量間的範圍。 預設值為10，支援的自訂值 `[2.0, 98.0]` 僅在範圍 (中 `ctukey`) 
* *max_percentile*：相同，預設值為90，支援的自訂值 `[2.0, 98.0]` 僅在範圍 (ctukey) 

下表描述與之間的 `"tukey"` 差異 `"ctukey"` ：

| 演算法 | 預設分量範圍 | 支援自訂分量範圍 |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25% / 75%              | 否                             |
| `"ctukey"`| 10% / 90%              | 是                            |

> [!TIP]
> 使用此函式的最佳方式，是將它套用至「 [製作系列](make-seriesoperator.md) 」運算子的結果。

## <a name="example"></a>範例

有一些雜訊的時間序列會建立極端值。 如果您想要以平均值 (雜訊) 來取代這些極端值，請使用 series_outliers ( # A3 來偵測極端值，然後加以取代。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" source="images/series-outliersfunction/series-outliers.png" alt-text="數列極端值" border="false":::
