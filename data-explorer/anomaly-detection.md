---
title: Azure 資料總管中 & 預測的時間序列異常偵測
description: 瞭解如何使用 Azure 資料總管分析時間序列資料，以進行異常偵測和預測。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: e48b3356d01248eb34857c936f9ff2c8dfb7662a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374001"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Azure 資料總管中的異常偵測和預測

Azure 資料總管會從雲端服務或 IoT 裝置執行遙測資料的持續收集。 此資料會針對各種深入解析進行分析，例如監視服務健全狀況、實體生產程式、使用趨勢和負載預測。 分析會在所選計量的時間序列上進行，以找出相對於其一般一般基準模式的度量偏差模式。 Azure 資料總管包含建立、操作及分析多個時間序列的原生支援。 它可以在幾秒內建立和分析數千個時間序列，讓您近乎即時地監視解決方案和工作流程。

本文詳細說明 Azure 資料總管時間序列異常偵測和預測功能。 適用的時間序列函數是以健全的知名分解模型為基礎，其中每個原始時間序列會分解成季節性、趨勢和剩餘元件。 在剩餘的元件上，極端值會偵測到異常，而預測則是藉由推斷季節性和趨勢元件來完成。 Azure 資料總管執行會透過自動季節性偵測、強大的極端情況分析和向量化的執行，大幅提升基本分解模型，以在數秒內處理數千個時間序列。

## <a name="prerequisites"></a>Prerequisites

如需時間序列功能的總覽，請參閱[Azure 資料總管中的時間序列分析](time-series-analysis.md)。

## <a name="time-series-decomposition-model"></a>時間序列分解模型

適用于時間序列預測和異常偵測的 Azure 資料總管原生執行會使用知名的分解模型。 此模型會套用至預期會列出定期和趨勢行為的時間序列，例如服務流量、元件偵測條件和 IoT 定期測量，以預測未來的計量值並偵測異常情況。 此回歸程式的假設是除了先前已知的季節性和趨勢行為之外，也會隨機散發時間序列。 然後，您可以從季節性和趨勢元件預測未來的計量值，並將其命名為基準，並忽略剩餘的部分。 您也可以只使用剩餘部分，根據極端值分析來偵測異常值。
若要建立分解模型，請使用函數 [`series_decompose()`](kusto/query/series-decomposefunction.md) 。 函式 `series_decompose()` 會採用一組時間序列，並自動會 decompose 每個時間序列的季節性、趨勢、剩餘和基準元件。 

例如，您可以使用下列查詢來分解內部 web 服務的流量：

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![時間序列分解](media/anomaly-detection/series-decompose-timechart.png)

* 原始時間序列的標籤為**num** （以紅色表示）。 
* 此程式一開始會使用函式自動偵測季節性 [`series_periods_detect()`](kusto/query/series-periods-detectfunction.md) ，並解壓縮**季節性**模式（紫色）。
* 季節性模式會從原始時間序列中減去，而線性回歸則是使用函數 [`series_fit_line()`](kusto/query/series-fit-linefunction.md) 來尋找**趨勢**元件（淺藍色）。
* 函式會減去趨勢，餘數則是**剩餘**的元件（以綠色表示）。
* 最後，函式會加入季節性和趨勢元件來產生**基準**（以藍色表示）。

## <a name="time-series-anomaly-detection"></a>時間序列異常偵測

函式會 [`series_decompose_anomalies()`](kusto/query/series-decompose-anomaliesfunction.md) 在一組時間序列上尋找異常點。 此函式會呼叫 `series_decompose()` 來建立分解模型，然後 [`series_outliers()`](kusto/query/series-outliersfunction.md) 在剩餘的元件上執行。 `series_outliers()`使用 Tukey 的隔離測試，計算剩餘元件每個點的異常分數。 高於1.5 或低於-1.5 的異常分數分別表示輕微的異常增加或拒絕。 高於3.0 或低於-3.0 的異常分數表示強性異常。 

下列查詢可讓您偵測內部 web 服務流量中的異常：

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![時間序列異常偵測](media/anomaly-detection/series-anomaly-detection.png)

* 原始的時間序列（以紅色表示）。 
* 基準（季節性 + 趨勢）元件（藍色）。
* 原始時間序列頂端的異常點（紫色）。 異常的點明顯偏離了預期的基準值。

## <a name="time-series-forecasting"></a>時間序列預測

函數會 [`series_decompose_forecast()`](kusto/query/series-decompose-forecastfunction.md) 預測一組時間序列的未來值。 這個函數會呼叫 `series_decompose()` 來建立分解模型，然後針對每個時間序列，將基準元件據此到未來。

下列查詢可讓您預測下一周的 web 服務流量：

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![時間序列預測](media/anomaly-detection/series-forecasting.png)

* 原始度量（以紅色表示）。 預設會遺漏未來的值，並將其設為0。
* 推斷基準元件（藍色）以預測下一周的值。

## <a name="scalability"></a>延展性

Azure 資料總管查詢語言語法可讓單一呼叫處理多個時間序列。 其獨特的優化執行可提供快速的效能，這對於在近乎即時的情況下監視數以千計的計數器時，對有效的異常偵測和預測很重要。

下列查詢會同時顯示三個時間序列的處理：

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![時間序列擴充性](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>總結

本檔詳述適用于時間序列異常偵測和預測的原生 Azure 資料總管功能。 每個原始時間序列會分解成季節性、趨勢和剩餘元件，以偵測異常和/或預測。 這些功能可用於近乎即時的監視案例，例如錯誤偵測、預測性維護，以及需求和負載預測。

## <a name="next-steps"></a>後續步驟

瞭解 Azure 資料總管中的[機器學習服務功能](machine-learning-clustering.md)。
