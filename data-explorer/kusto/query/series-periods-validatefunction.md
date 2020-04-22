---
title: series_periods_validate() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 series_periods_validate()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 8eba96e21513e776c984a356f88a705ca46485af
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663407"
---
# <a name="series_periods_validate"></a>series_periods_validate()

檢查時間序列是否包含給定長度的週期性模式。  

通常,衡量應用程式流量的指標的特點是每周和/或每日週期。 這可以通過`series_periods_validate()`運行 每周和每日期間的檢查來確認。

該函數將包含動態時間序列陣列(通常是[make 系列](make-seriesoperator.md)運算符的結果輸出)和定義要驗證的期間長度的一個或`real`多個數位作為輸入。 

函數輸出 2 欄:
* *句點*:包含要驗證的句點的動態陣列(在輸入中提供)
* *分數*: 包含介於 0 和 1 之間的分數的動態陣列,該陣列測量*期間在句點*陣列中各自位置的重要性

**語法**

`series_periods_validate(`*x* `,` *期間1* = `,` *期間2* `,` 。 . . ] `)`

**引數**

* *x*: 動態陣列標量表示式,它是數值陣列,通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算符的結果輸出。
* *期間1、**期間2*`real`等: 以箱大小為單位指定要驗證的期間數。 例如,如果序列位於 1h bin 中,則每周週期為 168 個條柱。

> [!IMPORTANT]
> * 每個*句點*參數的最小值為**4,** 最大值是輸入序列長度的一半;對對對這些邊界的*句點*參數,輸出分數將為**0**。
>
> * 輸入時間序列必須是常規的,即聚合在常量條柱中(如果已使用[make 系列](make-seriesoperator.md)創建,則始終如此)。 否則，輸出就沒有意義。
> 
> * 該函數最多接受 16 個驗證週期。


**範例**

以下查詢嵌入了應用程式流量一個月的快照,每天聚合兩次(即 bin 大小為 12 小時)。

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="系列期間":::

運行`series_periods_validate()`此系列以驗證每周週期(14 分長)會導致高分,驗證五天週期(10 分長)時得分**為 0。**

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| 序列\_\_期間\_\_驗證 y 期間  | 序列\_\_循環\_\_驗證 y 分數 |
|-------------|-------------------|
| [14.0, 10.0] | [0.84,0.0]  |