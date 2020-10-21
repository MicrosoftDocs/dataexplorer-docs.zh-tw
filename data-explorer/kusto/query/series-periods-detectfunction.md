---
title: 'series_periods_detect ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_periods_detect。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 6e362dd7d94c57cb79ee58c8ed7b36b719030987
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252941"
---
# <a name="series_periods_detect"></a>series_periods_detect()

尋找存在於時間序列中的最大週期。  

測量應用程式流量的計量通常是以兩個很長的週期來表徵：每週和每日。 函數會 `series_periods_detect()` 在時間序列中偵測這兩個主要期間。  
函數會採用作為輸入：
* 包含時間序列動態陣列的資料行。 一般來說，此資料行是 [成為數列](make-seriesoperator.md) 運算子的輸出結果。
* `real`定義最小和最大期間大小的兩個數字，也就是要搜尋的 bin 數目。 例如，針對1h 的 bin，每日期間的大小會是24。 
* `long`定義函數搜尋的總期間數的數位。 

此函數會輸出兩個數據行：
* *期間*：動態陣列，包含已找到的期間（以 bin 大小的單位排序），並依其分數排序。
* *分數*：動態陣列，包含介於0和1之間的值。 每個陣列會測量 *句號陣列中的某個期間的* 精確度。
 
## <a name="syntax"></a>語法

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

## <a name="arguments"></a>引數

* *x*：動態陣列純量運算式，這是數值的陣列，通常是 [構成數列](make-seriesoperator.md) 或 [make_list](makelist-aggfunction.md) 運算子的結果輸出。
* *min_period*： `real` 指定要搜尋之最短期間的數位。
* *max_period*： `real` 指定要搜尋之最長期間的數位。
* *num_periods*： `long` 指定所需最大期間數的數位。 這個數位會是輸出動態陣列的長度。

> [!IMPORTANT]
> * 演算法可以偵測到至少包含4個點以及數列長度最一半的期間。 
>
> * 設定以下的 *min_period* ，並 *max_period* 以上幾個您預期會在時間序列中找到的期間。 例如，如果您有每小時的匯總信號，並同時查看每日和每週期間 (24 和168小時分別) ，您可以設定 *min_period*= 0.8 \* 24、 *max_period*= 1.2 \* 168，並在這些期間內保留20% 的邊界。
>
> * 輸入時間序列必須是 regular。 亦即，在常數 bin 中匯總，如果是使用 [make 系列](make-seriesoperator.md)建立的，則一律為大小寫。 否則，輸出就沒有意義。

## <a name="example"></a>範例

下列查詢會內嵌應用程式流量的一個月快照集，每天匯總兩次。 Bin 大小為12小時。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="系列期間":::

在 `series_periods_detect()` 此系列上執行時，每週會產生14點的時間。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 數列 \_ 期間偵測 \_ 到 \_ y \_ 句號  | 數列 \_ 期間偵測 \_ 到 \_ y \_ 週期 \_ 分數 |
|-------------|-------------------|
| [14.0, 0.0] | [0.84，0.0]  |


> [!NOTE] 
> 找不到可能在圖表中看到的每日週期，因為取樣過於粗略 (12 小時的 bin 大小) ，因此每日2個 bin 的期間低於演算法所需的最小週期大小（4個點）。
