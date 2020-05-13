---
title: series_outliers （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_outliers （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 864638f8e03487a35eefa83fa3951d2ecefc27c7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372546"
---
# <a name="series_outliers"></a>series_outliers()

為數列中的異常點評分。

採用包含動態數值陣列做為輸入的運算式，並產生相同長度的動態數值陣列。 陣列的每個值都會使用[Tukey 的測試](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)指出可能的異常分數。 值大於 1.5 或小於-1.5，表示異常狀況分別在輸入的相同元素中上升或下降。   

**語法**

`series_outliers(`*x* `, `*種類* `, `*ignore_val* `, `*min_percentile* `, `*max_percentile*`)`

**引數**

* *x*：動態陣列資料格，也就是數值的陣列
* *種類：極端*偵測的演算法。 目前支援 `"tukey"` （傳統 Tukey）和 `"ctukey"` （自訂 Tukey）。 預設為 `"ctukey"`
* *ignore_val*：表示數列中遺漏值的數值，預設值為 double （null）。 Null 和 ignore 值的分數會設定為 `0` 。
* *min_percentile*：若為一般分量範圍的計算，預設值為10，支援的自訂值在範圍內 `[2.0, 98.0]` （ `ctukey` 僅限） 
* *max_percentile*：相同，預設值為90，支援的自訂值在範圍內 `[2.0, 98.0]` （僅限 ctukey） 

下表描述 `"tukey"` 與之間的差異 `"ctukey"` ：

| 演算法 | 預設分量範圍 | 支援自訂分量範圍 |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25% / 75%              | 否                             |
| `"ctukey"`| 10% / 90%              | 是                            |


> [!TIP]
> 使用此函式最方便的方式，是將它套用至[make 系列](make-seriesoperator.md)運算子的結果。

**範例**

假設您有一個時間序列，其中有一些雜訊會建立極端值，而您想要以平均值取代這些極端值（雜訊），您可以使用 series_outliers （）來偵測極端值，然後加以取代：

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
