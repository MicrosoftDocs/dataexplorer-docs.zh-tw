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
ms.openlocfilehash: 876966391e67ad2f8f25a900dfc4c92bf0bfd11e
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763267"
---
# <a name="series_periods_detect"></a>series_periods_detect()

尋找存在於時間序列中最重要的期間。  

測量應用程式流量的度量通常會有兩個重要的期間：每週和每天。 函式會 `series_periods_detect()` 在時間序列中偵測這兩個主要週期。  
函式會採用做為輸入：
* 包含時間序列動態陣列的資料行。 一般而言，資料行是[make 系列](make-seriesoperator.md)運算子的結果輸出。
* 兩個 `real` 數位，定義最小和最大週期大小，也就是要搜尋的 bin 數目。 例如，對於1h 的 bin，每日期間的大小會是24。 
* 數位，定義函式 `long` 要搜尋的總週期數。 

函式會輸出兩個數據行：
* *期間*：包含已找到之期間的動態陣列，以依分數排序的空間大小單位。
* *分數*：動態陣列，包含介於0到1之間的值。 每個陣列都會測量句點陣列中其各自位置的*某個期間的*重要性。
 
**語法**

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

**引數**

* *x*：動態陣列純量運算式，這是數值的陣列，通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出。
* *min_period*： `real` 指定要搜尋之最短期間的數位。
* *max_period*： `real` 指定要搜尋之最長期間的數位。
* *num_periods*： `long` 指定最大必要週期數的數位。 這個數位會是輸出動態陣列的長度。

> [!IMPORTANT]
> * 此演算法可以偵測到包含至少4個點和數列長度最多一半的期間。 
>
> * 請在下方設定*min_period* ，然後*max_period*您預期會在時間序列中找到的週期。 例如，如果您有每小時的匯總信號，而且您同時尋找每日和每週期間（分別為24和168小時），您可以設定*min_period*= 0.8 \* 24， *max_period*= 1.2 \* 168，並在這段期間保留20% 的邊界。
>
> * 輸入時間序列必須是一般的。 也就是，在常數的 bin 中匯總，如果它是使用[make 系列](make-seriesoperator.md)所建立的，則一律是這樣。 否則，輸出就沒有意義。

**範例**

下列查詢會內嵌應用程式流量的一個月快照集，每日匯總兩次。 Bin 大小為12小時。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="數列期間":::

`series_periods_detect()`在此系列上執行，結果為每週期間，14點長。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 數列 \_ 期間偵測 \_ 到 \_ y \_ 週期  | 數列 \_ 期間偵測 \_ 到 \_ y \_ 週期 \_ 分數 |
|-------------|-------------------|
| [14.0, 0.0] | [0.84，0.0]  |


> [!NOTE] 
> 找不到圖表中可能也會出現的每日期間，因為取樣太過粗糙（12h 的 bin 大小），因此每日2個區間的最小週期大小低於4個點，這是演算法所需。
