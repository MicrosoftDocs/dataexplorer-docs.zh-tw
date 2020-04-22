---
title: 在 Azure 資料資源管理器中,時間序列異常檢測&預測
description: 瞭解如何使用 Azure 資料資源管理器分析時間序列數據以進行異常檢測和預測。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: eac1237e098b90f526e441ef89f0902f72abde53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496702"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Azure 資料資源管理員中的例外檢測和預測

Azure 資料資源管理器執行來自雲服務或IoT設備的遙測數據持續收集。 分析此數據以獲得各種見解,例如監視服務運行狀況、物理生產流程、使用趨勢和負載預測。 分析是在所選指標的時間序列上進行的,以找到指標相對於其典型正常基線模式的偏差模式。 Azure 資料資源管理員包含對創建、操作和分析多個時間序列的本機支援。 它可以在幾秒鐘內創建和分析數千個時間序列,實現近乎即時的監控解決方案和工作流。

本文詳細介紹了 Azure 數據資源管理器時間序列異常檢測和預測功能。 適用的時間序列函數基於一個健壯的眾所周知的分解模型,其中每個原始時間序列分解為季節性、趨勢和殘餘分量。 異常由殘餘分量上的異常值檢測,而預測是通過推斷季節性和趨勢分量來完成的。 Azure 資料資源管理器實現通過自動季節性檢測、可靠的異常值分析和矢量化實現顯著增強基本分解模型,從而在幾秒鐘內處理數千個時間序列。

## <a name="prerequisites"></a>Prerequisites

在[Azure 資料資源管理器中讀取時間序列分析](/azure/data-explorer/time-series-analysis),瞭解時間序列功能的概述。

## <a name="time-series-decomposition-model"></a>時間序列分解模型

Azure 資料資源管理器本機實現用於時間序列預測和異常檢測,使用眾所周知的分解模型。 此模型應用於預期要體現週期性和趨勢行為的指標時間序列,例如服務流量、元件檢測信號和IoT定期測量,以預測未來指標值並檢測異常值。 此回歸過程的假設是,除了以前已知的季節性和趨勢行為之外,時間序列是隨機分佈的。 然後,您可以預測季節性和趨勢成分的未來指標值,統稱為基線,並忽略剩餘部分。 您還可以使用僅使用剩餘部分的異常值分析來檢測異常值。
要建立分解模型,請使用函數[`series_decompose()`](kusto/query/series-decomposefunction.md)。 該`series_decompose()`函數採用一組時間序列,並自動將每個時間序列分解為其季節性、趨勢、剩餘和基線分量。 

例如,可以使用以下查詢分解內部 Web 服務的流量:

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

* 原始時間序列標記為**num(** 紅色)。 
* 該過程首先使用 函[`series_periods_detect()`](kusto/query/series-periods-detectfunction.md)數 自動檢測季節性,並提取**季節性**模式(紫色)。
* 季節性模式從原始時間序列中減去,並使用 函[`series_fit_line()`](kusto/query/series-fit-linefunction.md)數 運行線性回歸以查找**趨勢**分量(淺藍色)。
* 函數減去趨勢,其餘部分為**剩餘**分量(綠色)。
* 最後,函數添加季節性和趨勢分量以生成**基線**(藍色)。

## <a name="time-series-anomaly-detection"></a>時間序列異常偵測

函數[`series_decompose_anomalies()`](kusto/query/series-decompose-anomaliesfunction.md)在一組時間序列上查找異常點。 此函數調用`series_decompose()`以生成分解模型,然後在剩餘元件[`series_outliers()`](kusto/query/series-outliersfunction.md)上運行。 `series_outliers()`使用圖基的圍欄測試計算剩餘分量的每個點的異常分數。 異常分數高於1.5或低於-1.5表示異常值分別出現輕微上升或下降。 異常分數高於 3.0 或低於 -3.0 表示異常性強。 

以下查詢允許您偵測內部 Web 服務流量中的異常:

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

* 原始時間序列(紅色)。 
* 基線(季節性 + 趨勢)元件(藍色)。
* 原始時間序列頂部的異常點(紫色)。 異常點明顯偏離預期的基線值。

## <a name="time-series-forecasting"></a>時間序列預測

該函數[`series_decompose_forecast()`](kusto/query/series-decompose-forecastfunction.md)預測一組時間序列的未來值。 此函數調用`series_decompose()`以構建分解模型,然後為每個時間序列推斷基線元件到將來。

以下查詢允許您預測下週的 Web 服務流量:

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

* 原始指標(紅色)。 默認情況下,缺少將來的值並設置為 0。
* 推斷基線元件(藍色),以預測下周的值。

## <a name="scalability"></a>延展性

Azure 資料資源管理員查詢語言語法允許單個調用來處理多個時間序列。 其獨特的優化實現可實現快速性能,這對於在近即時方案中監控數千個計數器時有效的異常檢測和預測至關重要。

以下查詢同時顯示了三個時間序列的處理:

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

![時間序列可擴充性](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>摘要

本文件詳細介紹了本機 Azure 數據資源管理器功能,用於時間序列異常檢測和預測。 每個原始時間序列被分解為季節性、趨勢和殘餘成分,用於檢測異常和/或預測。 這些功能可用於近即時監視方案,如故障檢測、預測性維護以及需求和負載預測。

## <a name="next-steps"></a>後續步驟

瞭解 Azure 資料資源管理員中的[機器學習功能](/azure/data-explorer/machine-learning-clustering)。
