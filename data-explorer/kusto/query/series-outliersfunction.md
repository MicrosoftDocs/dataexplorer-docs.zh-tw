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
ms.openlocfilehash: c5ada33d74f5ed3e1c7b357321b23bd7a76be64e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351347"
---
# <a name="series_outliers"></a>series_outliers()

為數列中的異常點評分。

函式會採用包含動態數值陣列的運算式做為輸入，並產生相同長度的動態數值陣列。 陣列的每個值都會使用「 [Tukey 的測試](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)」來表示可能發生異常的分數。 在輸入的相同元素中，大於1.5 的值表示增加或拒絕異常。 小於-1.5 的值表示拒絕異常。

## <a name="syntax"></a>語法

`series_outliers(`*x* `, `*種類* `, `*ignore_val* `, `*min_percentile* `, `*max_percentile*`)`

## <a name="arguments"></a>引數

* *x*：動態陣列資料格，其為數值陣列
* *種類：極端*偵測的演算法。 目前支援 `"tukey"` （傳統的 "Tukey"）和 `"ctukey"` （自訂的 "Tukey"）。 預設為 `"ctukey"`
* *ignore_val*：表示數列中遺漏值的數值。 預設值為 double （null）。 Null 和 ignore 值的分數設定為`0`
* *min_percentile*：用於計算分量間的標準範圍。 預設值為10，支援的自訂值在範圍內 `[2.0, 98.0]` （ `ctukey` 僅限）
* *max_percentile*：相同，預設值為90，支援的自訂值在範圍內 `[2.0, 98.0]` （僅限 ctukey）

下表描述 `"tukey"` 與之間的差異 `"ctukey"` ：

| 演算法 | 預設分量範圍 | 支援自訂分量範圍 |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25% / 75%              | 否                             |
| `"ctukey"`| 10% / 90%              | 是                            |

> [!TIP]
> 使用此函式的最佳方式是將它套用至[make 系列](make-seriesoperator.md)運算子的結果。

## <a name="example"></a>範例

有一些雜訊的時間序列會建立極端值。 如果您想要將這些極端值（雜訊）取代為平均值，請使用 series_outliers （）來偵測極端值，然後加以取代。

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
