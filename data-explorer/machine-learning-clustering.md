---
title: Azure 資料總管中的機器學習功能
description: 使用機器學習叢集來進行 Azure 資料總管中的根本原因分析。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 7825f7fad16d8194d1e97008e9958d30ca20ddd5
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875152"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Azure 資料總管中的機器學習功能

Azure 資料總管是很大的資料分析平臺。 它是用來監視服務健康情況、QoS 或故障的裝置。 內建的 [異常偵測和預測](anomaly-detection.md) 函數會檢查異常行為。 一旦偵測到這種模式之後，就會執行根本原因分析 (RCA) 來減緩或解決異常狀況。

診斷程式很複雜且冗長，並由領域專家完成。 此程序包括：
* 在相同的時間範圍內，從不同的來源提取和聯結其他資料
* 在多個維度上尋找值分佈的變更
* 製作其他變數的圖表
* 以領域知識和直覺為基礎的其他技術

因為這些診斷案例在 Azure 資料總管中很常見，所以機器學習外掛程式可讓診斷階段更輕鬆，並縮短 RCA 的持續時間。

Azure 資料總管有三個 Machine Learning 外掛程式： [`autocluster`](kusto/query/autoclusterplugin.md) 、 [`basket`](kusto/query/basketplugin.md) 和 [`diffpatterns`](kusto/query/diffpatternsplugin.md) 。 所有外掛程式都會執行群集演算法。 `autocluster`和 `basket` 外掛程式叢集單一記錄集，而外掛程式會叢集 `diffpatterns` 兩個記錄集之間的差異。

## <a name="clustering-a-single-record-set"></a>群集單一記錄集

常見的案例包括特定準則所選取的資料集，例如：
* 顯示異常行為的時間範圍
* 高溫度裝置讀數
* 長時間的命令
* 您想要快速且輕鬆地在資料中尋找 (區段) 常見模式的最高費用使用者。 模式是資料集的子集，其記錄會在多個維度之間共用相同的值， (分類資料行) 。 

下列查詢會建立並顯示在一週期間內的服務例外狀況（以10分鐘為單位）的時間序列：

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![服務例外狀況時間圖表](media/machine-learning-clustering/service-exceptions-timechart.png)

服務例外狀況計數會與整體服務流量相關聯。 您可以清楚地看到工作天的每日模式，星期一到星期五。 中間的服務例外狀況計數有增加，並會在夜間的計數中捨棄。 一般的低計數會在週末顯示。 在 Azure 資料總管中使用 [時間序列異常偵測](anomaly-detection.md#time-series-anomaly-detection) ，即可偵測到例外狀況尖峰。

資料的第二個尖峰會在星期四下午進行。 下列查詢可用來進一步診斷並確認它是否為明顯尖峰。 此查詢會在一分鐘的時間內，以較高的解析度，以較高的解析度來重繪圖表。然後您就可以研究其框線。

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=)**\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![專注于尖峰時間圖表](media/machine-learning-clustering/focus-spike-timechart.png)

您會看到從15:00 到15:02 的窄兩分鐘尖峰。 在下列查詢中，計算此兩分鐘時間範圍內的例外狀況：

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

在下列查詢中，範例20個例外狀況（972）：

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | 區域 | ScaleUnit | DeploymentId                     | 點 | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15：00：08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15：00：09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15：00：10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15：00：12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15：00：18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15：00：20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15：00：23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15：00：26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15：00：27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15：00：32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15：00：34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15：00：37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15：00：41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15：00：47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15：00：50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15：00：50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15：00：53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15：00：57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15：00：58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15：00：59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>針對單一記錄集叢集使用 autocluster ( # A1

即使沒有超過一千個例外狀況，仍很難找到常見的區段，因為每個資料行中都有多個值。 您可以使用 [`autocluster()`](kusto/query/autoclusterplugin.md) 外掛程式立即解壓縮常用區段的簡短清單，然後在尖峰的兩分鐘內找出感興趣的叢集，如下列查詢所示：

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Count | 百分比 | 區域 | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 專案 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

您可以從上述結果看到，最主要的區段包含總例外狀況記錄的65.74%，並共用四個維度。 下一個區段較不常見。 它只包含9.67% 的記錄，並共用三個維度。 其他區段則更不常見。

Autocluster 會使用專屬演算法來挖掘多個維度並將有趣的區段解壓縮。 「有趣的」表示每個區段都有大量的記錄集和功能集的涵蓋範圍。 這些區段也會分歧，這表示每個區段與其他區段不同。 這些區段中的一或多個可能與 RCA 程式相關。 為了將區段審核和評量降至最低，autocluster 只會解壓縮一個小的區段清單。

### <a name="use-basket-for-single-record-set-clustering"></a>針對單一記錄集叢集使用購物籃 ( # A1

您也可以使用 [`basket()`](kusto/query/basketplugin.md) 下列查詢中所示的外掛程式：

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Count | 百分比 | 區域 | ScaleUnit | DeploymentId | 點 | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 專案 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 專案 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 專案 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 專案 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

購物籃會針對專案集的挖掘實行「 *Apriori* 」演算法。 它會將記錄集涵蓋範圍的所有區段解壓縮 (預設為 5% ) 。 您可以看到有更多區段以類似的區段解壓縮，例如區段0、1或2、3。

這兩個外掛程式都有強大且容易使用的功能。 其限制是它們會以非監督式的方式（沒有標籤）來叢集單一記錄集。 如果已解壓縮的模式會將選取的記錄集、異常記錄或全域記錄集的特性表示為不清楚。

## <a name="clustering-the-difference-between-two-records-sets"></a>群集兩個記錄集之間的差異

[`diffpatterns()`](kusto/query/diffpatternsplugin.md)外掛程式克服了和的限制 `autocluster` `basket` 。 `Diffpatterns` 採用兩個記錄集，並解壓縮不同的主要區段。 其中一個集合通常包含正在調查的異常記錄集。 其中一個是由 `autocluster` 和所分析 `basket` 。 另一組則包含參考記錄集，也就是基準。

在下列查詢中， `diffpatterns` 尋找尖峰的兩分鐘內有興趣的叢集，與基準中的群集不同。 當尖峰開始時，基準視窗會定義為15:00 之前的八分鐘。 您可以利用二進位資料行來擴充 (AB) ，並指定特定記錄是否屬於基準或異常集合。 `Diffpatterns` 實行受監督的學習演算法，其中兩個類別標籤是由異常和基準旗標所產生 (AB) 。

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | 區域 | ScaleUnit | DeploymentId | 點 |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 專案 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

最主要的區段是所解壓縮的相同區段 `autocluster` 。 在兩分鐘的異常時段內，其涵蓋範圍也是65.74%。 不過，在八分鐘的基準視窗上，其涵蓋範圍僅為1.7%。 差異為64.04%。 這種差異似乎與異常尖峰相關。 若要確認這種假設，請將原始圖表分割成屬於這個有問題之區段的記錄，以及來自其他區段的記錄。 請參閱下列查詢。

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![正在驗證 ' diffpattern ' 區段時間圖表](media/machine-learning-clustering/validating-diffpattern-timechart.png)

此圖表可讓我們看到星期二下午的尖峰是因為這個特定區段的例外狀況，使用外掛程式探索到 `diffpatterns` 。

## <a name="summary"></a>摘要

Azure 資料總管 Machine Learning 外掛程式對於許多案例都很有説明。 `autocluster`和 `basket` 會執行非監督式學習演算法，而且很容易使用。 `Diffpatterns` 實作為受監督的學習演算法，雖然更複雜，但更能針對 RCA 的差異區段進行解壓縮。

這些外掛程式會以互動方式在臨機操作案例中使用，以及在自動近乎即時的監視服務中使用。 在 Azure 資料總管中，時間序列異常偵測會接著診斷程式。 此程式經過高度優化，以符合必要的效能標準。
