---
title: series_periods_detect （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_periods_detect （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: a3f2a325b63306f7fec6b11eb3e684d3918bc7d5
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372532"
---
# <a name="series_periods_detect"></a>series_periods_detect()

尋找存在於時間序列中最重要的期間。  

測量應用程式流量的度量通常會有兩個重要的週期：每週和每天。 假設有這種時間序列，應該會偵測 `series_periods_detect()` 到這兩個主要期間。  
函式會將包含時間序列動態陣列的資料行（通常是[make 系列](make-seriesoperator.md)運算子的結果輸出）做為輸入，並使用兩個 `real` 數位來定義最小和最大週期大小（也就是 bin 的數目，例如 for 1h bin 每日週期的大小為24）來搜尋，以及定義函式 `long` 要搜尋的總期間數的數位。 函式會輸出2個數據行：
* *期間*：包含已找到之期間的動態陣列（以 bin 大小的單位），依分數排序
* *分數*：包含0和1之間值的動態陣列，每個都測量週期陣列中其各自位置的*某個期間的*重要性
 
**語法**

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

**引數**

* *x*：動態陣列純量運算式，這是數值的陣列，通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出。
* *min_period*： `real` 指定要搜尋之最短期間的數位。
* *max_period*： `real` 指定要搜尋之最長期間的數位。
* *num_periods*： `long` 指定最大必要週期數的數位。 這會是輸出動態陣列的長度。

> [!IMPORTANT]
> * 此演算法可以偵測到包含至少4個點和數列長度最多一半的期間。 
>
> * 您應該將*min_period*設定為以下部分，並在您預期於時間序列中找到的期間*max_period*一點。 例如，如果您有每小時匯總的信號，而且您同時尋找每日 > 和每週期間（分別為 24 & 168），您可以設定*min_period*= 0.8 \* 24， *max_period*= 1.2 \* 168，將20% 的邊界留在這段期間。
>
> * 輸入時間序列必須是一般的，也就是在常數 bin 中匯總（如果是使用[make 系列](make-seriesoperator.md)所建立的，則一律為這種情況）。 否則，輸出就沒有意義。


**範例**

下列查詢會內嵌應用程式流量的一個月快照集，每日匯總兩次（也就是，bin 大小是12小時）。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="數列期間":::

`series_periods_detect()`在此系列上執行會導致每週期間（14點長）：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 數列 \_ 期間偵測 \_ 到 \_ y \_ 週期  | 數列 \_ 期間偵測 \_ 到 \_ y \_ 週期 \_ 分數 |
|-------------|-------------------|
| [14.0, 0.0] | [0.84，0.0]  |


請注意，在圖表中可能也會看到的每日期間都找不到，因為取樣太過粗略（12h 的 bin 大小），因此每日2個區間的鈴是演算法所需之4點的最小週期大小。
