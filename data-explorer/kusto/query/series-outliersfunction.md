---
title: series_outliers() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 azure 數據資源管理器中的 series_outliers()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 47e479ce7fe09b2456405ac3f7daed4721374f32
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663450"
---
# <a name="series_outliers"></a>series_outliers()

在序列中得分異常點。

以包含動態數值陣列的運算式作為輸入,並生成長度相同的動態數值陣列。 陣列的每個值都使用[圖基的測試](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)指示可能的異常分數。 值大於 1.5 或小於-1.5，表示異常狀況分別在輸入的相同元素中上升或下降。   

**語法**

`series_outliers(`*x*`, `*類別*`, `*min_percentile*ignore_valmin_percentilemax_percentile* * `, ` `, ` * *`)`

**引數**

* *x*: 動態陣列儲存格,這是數值陣列
* *類型*: 異常值偵測演算法。 當前支援`"tukey"`(傳統圖基)`"ctukey"`和 (自訂圖基)。 預設為 `"ctukey"`
* *ignore_val*: 表示序列中缺少值的數值,預設值為雙精度(空)。 null 與 ignore 值的分`0`數設定為 。
* *min_percentile:* 對於普通分位數間範圍的計算,預設值為 10,支援的自定義值`[2.0, 98.0]`在`ctukey`範圍內 ( 僅) 
* *max_percentile*:相同,預設值為 90,支援的自訂`[2.0, 98.0]`值在 範圍內(僅限 ctukey) 

下表描述了`"tukey"``"ctukey"`與之間的差異:

| 演算法 | 預設分量範圍 | 支援自訂分量範圍 |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25% / 75%              | 否                             |
| `"ctukey"`| 10% / 90%              | 是                            |


> [!TIP]
> 使用此功能最方便的方法是將其應用於[製造系列](make-seriesoperator.md)運算符的結果。

**範例**

假設您有一個具有一些雜訊的時間序列,該時間序列會產生異常值,並且您希望將這些異常值(雜訊)替換為平均值,則可以使用 series_outliers() 來檢測異常值,然後替換它們:

```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" border="false" source="images/samples/series-outliers.png" alt-text="系列例外值":::
