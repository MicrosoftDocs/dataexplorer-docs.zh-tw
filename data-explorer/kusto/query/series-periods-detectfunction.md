---
title: series_periods_detect() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_periods_detect()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 0bc78f93270809774504c1eccbc9fa2bbce6c964
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663434"
---
# <a name="series_periods_detect"></a>series_periods_detect()

查找時間序列中存在的最重要期間。  

通常,衡量應用程式流量的指標具有兩個重要時間段:每周和每日。 給定這樣的時間序列,`series_periods_detect()`應偵測這兩個主導期。  
該函數將包含動態時間序列陣列(通常是[make 系列](make-seriesoperator.md)運算符的結果輸出)的列作為輸入,將`real`兩個數位定義為最小和最大週期大小(即 bin 數,例如對於 1h bin,每日週期的大小為 24),以及定義要搜索的`long`函數的總週期數的數位。 函數輸出 2 欄:
* *句點*:包含已找到的句點(以 bin 大小的單位)的動態陣列,按其分數排序
* *分數*: 包含介於 0 與 1 之間的值的動態陣列,每個陣列測量*期間在句點*陣列中各自位置的重要性
 
**語法**

`series_periods_detect(`*x* `,` x `,` `,` min_periodmax_periodnum_periods *num_periods* *max_period* *min_period*`)`

**引數**

* *x*: 動態陣列標量表示式,它是數值陣列,通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算符的結果輸出。
* *min_period*`real`: 指定要搜尋的最小期間的數字。
* *max_period*`real`: 指定要搜尋的最大週期的數字。
* *num_periods*`long`: 指定最大所需句點數的數字。 這將是輸出動態陣列的長度。

> [!IMPORTANT]
> * 該演演演算法可以檢測至少 4 個點和最多一半的序列長度的週期。 
>
> * 您應該將*min_period*設定在下面一點 *,max_period*比預期在時間序列中找到的期間稍高一點。 例如,如果您有每小時聚合的信號,並且查找每日>和每周週期(分別為 24 & 168),*min_period*則可以設置 min_period\*±0.8 24,max_period =1.2 * * \*168,在這些期間周圍留下 20% 的邊距。
>
> * 輸入時間序列必須是常規的,即聚合在常量條柱中(如果已使用[make 系列](make-seriesoperator.md)創建,則始終如此)。 否則，輸出就沒有意義。


**範例**

以下查詢嵌入了應用程式流量一個月的快照,每天聚合兩次(即 bin 大小為 12 小時)。

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="系列期間":::

執行`series_periods_detect()`此系列會導致每週週期(14 點長):

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 序列\_\_循環\_\_偵測 y 週期  | 序列\_\_循環\_\_中\_測 y 週期分數 |
|-------------|-------------------|
| [14.0, 0.0] | [0.84, 0.0]  |


請注意,由於採樣過於粗糙(12h bin 大小),因此每日 2 個條柱的週期是演演演算法要求的最小週期大小 4 點,因此找不到圖表中也能看到的每日週期週期。