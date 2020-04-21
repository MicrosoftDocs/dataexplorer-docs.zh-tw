---
title: Azure 資料資源管理員中的機器學習功能
description: 在 Azure 數據資源管理器中使用機器學習群集進行根本原因分析。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: e2adedec36696c7974ecb2e7f7c62d65329c368f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502344"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Azure 資料資源管理員中的機器學習功能

Azure 資料資源管理員(一個大數據分析平臺)使用內置[異常檢測和預測](/azure/data-explorer/anomaly-detection)功能監視服務運行狀況、QoS 或故障設備中的異常行為。 檢測到異常模式后,將執行根本原因分析 (RCA) 以緩解或解決異常。

診斷過程複雜而漫長,由領域專家完成。 該過程包括在同一時間範圍內從不同來源提取和聯接其他數據,查找多個維度上值分佈的變化,繪製其他變數的圖表,以及基於域知識和直覺的其他技術。 由於這些診斷方案在 Azure 資料資源管理器中很常見,因此機器學習外掛程式可用於簡化診斷階段並縮短 RCA 的持續時間。

Azure 資料資源管理員有三個機器學習外掛[`autocluster`](kusto/query/autoclusterplugin.md)[`basket`](kusto/query/basketplugin.md)程式[`diffpatterns`](kusto/query/diffpatternsplugin.md): 和 。 所有外掛程式都實現聚類演演演算法。 和`autocluster``basket`外掛程式叢集單個紀錄`diffpatterns`集, 外掛程式群集兩個記錄集之間的差異。

## <a name="clustering-a-single-record-set"></a>叢集單個記錄集

常見方案包括由特定條件(如顯示異常行為的時間視窗、高溫設備讀數、長時間命令和頂級支出使用者)選擇的數據集。 我們希望有一種簡單快捷的方法在數據中找到常見模式(段)。 模式是數據集的子集,其記錄在多個維度(分類列)上共用相同的值。 以下查詢在十分鐘的條柱中產生並顯示一周中的服務異常時間序列:

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![服務例外時程表](media/machine-learning-clustering/service-exceptions-timechart.png)

服務異常計數與整個服務流量相關。 您可以清楚地看到每日模式,對於週一至週五的營業日,服務異常增加計入中午,夜間計數下降。 平低計數在週末可見。 可以使用 Azure 資料資源管理器中的[時序異常檢測](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection)來檢測異常峰值。

數據的第二個高峰發生在週二下午。 以下查詢用於進一步診斷此峰值。 使用查詢在高解析度的峰值(一分鐘內的 bin 中為 8 小時)重繪圖表,以驗證它是否是尖峰,並查看其邊框。

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=)**\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![專注於峰值時程圖表](media/machine-learning-clustering/focus-spike-timechart.png)

我們看到從15:00到15:02的兩分鐘窄峰值。 在以下查詢中,計算此兩分鐘視窗中的異常:

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

在下面的查詢中,972 中的 20 個異常示例:

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | 區域 | 規模單位 | DeploymentId                     | 追蹤點 | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aee40d7e51 | 36109      | 1942243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | 恩庫斯   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | 恩庫斯   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d00e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aee40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>將自動叢集()用於單個記錄集叢集

即使存在少於一千個異常,仍很難找到公共段,因為每列中有多個值。 您可以使用[`autocluster()`](kusto/query/autoclusterplugin.md)外掛程式立即提取一小小份常見欄位清單,並在峰值的兩分鐘內找到有趣的群集,如以下查詢所示:

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Count | 百分比 | 區域 | 規模單位 | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | 歐 | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | 恩庫斯 | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | 韋烏 | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

從上面的結果可以看出,最具支配力的段包含總異常記錄的 65.74%,並共用四個維度。 下一段不太常見,僅包含 9.67% 的記錄,並共用三個維度。 其他段更不常見。 

自動群集使用專有演演演算法挖掘多個維度並提取有趣的段。 "有趣"表示每個段都有記錄集和功能集的顯著覆蓋範圍。 這些段也存在分歧,這意味著每個段都與其他段明顯不同。 其中一個或多個段可能與RCA流程相關。 為了最小化段審核和評估,自動群集僅提取一個小段清單。

### <a name="use-basket-for-single-record-set-clustering"></a>使用購物籃()進行單個記錄集群集

您還可以使用外掛程式[`basket()`](kusto/query/basketplugin.md), 如以下查詢所示:

**\[**[**按一下以執行查詢**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Count | 百分比 | 區域 | 規模單位 | DeploymentId | 追蹤點 | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | 歐 | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | 歐 | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | 歐 | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | 歐 | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | 恩庫斯 | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | 韋烏 | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Basket 為專案集挖掘實現 Apriori 演演演算法,並提取記錄集覆蓋率高於閾值(預設 5%)的所有段。 您可以看到,使用類似的段(例如,線段 0、1 或 2,3)提取了更多段。

這兩個外掛程式功能強大且易於使用,但它們的重要限制是,它們以不受監督的方式(沒有標籤)對單個記錄進行群集。 因此,不清楚提取的模式是所選記錄集(異常記錄)的特徵還是全域記錄集的特徵。

## <a name="clustering-the-difference-between-two-records-sets"></a>叢集兩個紀錄集之間的差異

外掛[`diffpatterns()`](kusto/query/diffpatternsplugin.md)程式克服了`autocluster``basket`和的限制。 `Diffpatterns`獲取兩個記錄集並提取它們之間不同的主段。 一個集通常包含正在調查的異常記錄集(由`autocluster``basket`和分析的一個記錄集)。 另一個集包含引用記錄集(基線)。 

在下面的查詢中,我們用於`diffpatterns`在尖峰的兩分鐘內查找有趣的群集,這些群集與基線中的群集不同。 我們將基線視窗定義為 15:00 之前的 8 分鐘(峰值開始)。 我們還需要通過二進位列 (AB) 進行擴展,指定特定記錄是屬於基線還是異常集。 `Diffpatterns`實現受監督的學習演演演算法,其中兩個類標籤由異常與基線標誌 (AB) 生成。

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

| SegmentId | CountA | CountB | PercentA | PercentB | 百分比迪法 | 區域 | 規模單位 | DeploymentId | 追蹤點 |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | 歐 | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | 恩庫斯 | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | 韋烏 | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

最具優勢的細分市場是提取的同一段`autocluster`,其在兩分鐘異常視窗上的覆蓋率也為65.74%。 但它在8分鐘基線視窗的覆蓋率只有1.7%。 差價為64.04%。 這種差異似乎與異常峰值有關。 通過將原始圖表拆分為屬於此有問題的段的記錄與其他細分的記錄(如下查詢所示),可以驗證此假設:

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

![驗證「差異模式」段時間圖](media/machine-learning-clustering/validating-diffpattern-timechart.png)

此圖表允許我們看到,週二下午的峰值是由於此特定段的異常,使用`diffpatterns`外掛程式發現。

## <a name="summary"></a>摘要

Azure 數據資源管理器機器學習外掛程式對於許多方案都很有用。 和`autocluster``basket`實現無監督學習演演演算法,易於使用。 `Diffpatterns`實現有監督的學習演演演算法,雖然更為複雜,但它在提取RCA的分化段方面更強大。

這些外掛程式在臨時方案和自動近即時監視服務中以交互方式使用。 在 Azure 資料資源管理器中,時間序列異常檢測之後是經過高度優化以滿足必要性能標準的診斷過程。
