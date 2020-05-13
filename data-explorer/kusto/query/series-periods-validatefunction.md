---
title: series_periods_validate （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_periods_validate （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: b157c7fef0b9b4d98f08f5e5020803eea3960097
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372489"
---
# <a name="series_periods_validate"></a>series_periods_validate()

檢查時間序列是否包含指定長度的定期模式。  

測量應用程式流量的計量通常是以每週和/或每日週期為特性。 這可以藉由執行 `series_periods_validate()` 每週和每天的檢查來確認。

函式會將包含時間序列動態陣列的資料行（通常是[make 系列](make-seriesoperator.md)運算子的結果輸出）和一或多個 `real` 數位（定義要驗證的期間長度）當做輸入。 

函式會輸出2個數據行：
* *期間*：包含要驗證之期間的動態陣列（在輸入中提供）
* *分數*：動態陣列，其中包含0和1之間的分數，以測量週期陣列中其各自位置的*某個期間的*重要性

**語法**

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* ] `,` 。 . . ] `)`

**引數**

* *x*：動態陣列純量運算式，這是數值的陣列，通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出。
* *period1*、 *period2*等： `real` 用來指定要驗證之期間的數位（以 bin 大小的單位表示）。 例如，如果數列位於1h 的 bin 中，則每週期間為168的 bin。

> [!IMPORTANT]
> * 每個*period*引數的最小值為**4** ，而上限為輸入數列長度的一半;對於超出這些界限的*period*引數，輸出分數將為**0**。
>
> * 輸入時間序列必須是一般的，也就是在常數 bin 中匯總（如果是使用[make 系列](make-seriesoperator.md)所建立的，則一律為這種情況）。 否則，輸出就沒有意義。
> 
> * 函式最多可接受16個週期來進行驗證。


**範例**

下列查詢會內嵌應用程式流量的一個月快照集，每日匯總兩次（也就是，bin 大小是12小時）。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="數列期間":::

`series_periods_validate()`在此系列上執行，以驗證每週（14個點長）的分數，而在驗證五天的期間（10點長）時，分數為**0** 。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| 數列 \_ 週期 \_ 驗證 \_ y \_ 期間  | 數列 \_ 週期 \_ 驗證 \_ y \_ 分數 |
|-------------|-------------------|
| [14.0，10.0] | [0.84，0.0]  |
