---
title: series_seasonal （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_seasonal （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 054d4be758001609fbc3100a4a6c8698ef8f69f6
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717303"
---
# <a name="series_seasonal"></a>series_seasonal()

根據偵測到或指定的季節性週期，計算數列的季節性元件。

**語法**

`series_seasonal(`*數列* `[,`*期間*`])`

**引數**

* *數列*：輸入數值動態陣列
* *period* （選擇性）：每個季節性期間中的每個區間的整數數目，可能的值：
    *  -1 （預設值）：使用[series_periods_detect （）](series-periods-detectfunction.md)與閾值*0.7*來自動偵測 dll 週期。 如果未偵測到季節性，則傳回零
    * 正整數：用來做為季節性元件的期間
    * 任何其他值：忽略季節性，並傳回一系列的零

**傳回**

動態陣列，其長度與包含數列之計算季節性元件的*數列*輸入相同。 季節性元件的計算方式是將所有值對應至整個*期間的 bin*位置。

## <a name="examples"></a>範例

### <a name="auto-detect-the-period"></a>自動偵測期間

在下列範例中，會自動偵測數列的期間。 第一個數列的期間會被偵測為六個區間和第二個五個區間。第三個數列的期間太短，無法偵測並傳回一系列的零。 請參閱下一個範例，[以瞭解如何強制執行此期間](#force-a-period)。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2，5，3，4，3，2，1，2，3，4，3，2，1，2，3，4，3，2，1，2，3，4，3，2，1]|[1.0，2.0，3.0，4.0，3.0，2.0，1.0，2.0，3.0，4.0，3.0，2.0，1.0，2.0，3.0，4.0，3.0，2.0，1.0，2.0，3.0，4.0，3.0，2.0，1.0]|
|[8、12、14、12、10、10、12、14、12、10、10、12、14、12、10、10、12、14、12、10]|[10.0，12.0，14.0，12.0，10.0，10.0，12.0，14.0，12.0，10.0，10.0，12.0，14.0，12.0，10.0，10.0，12.0，14.0，12.0，10.0]|
|[1，3，5，2，4，6，1，3，5，2，4，6]|[0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0]|

### <a name="force-a-period"></a>強制執行句點

在此範例中，序列的期間太短，無法由[series_periods_detect （）](series-periods-detectfunction.md)偵測到，所以我們會明確強制該期間取得季節性模式。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1，3，5，1，3，5，2，4，6]|[1.0，3.0，5.0，1.0，3.0，5.0，1.0，3.0，5.0]|
|[1，3，5，2，4，6，1，3，5，2，4，6]|[1.5，3.5，5.5，1.5，3.5，5.5，1.5，3.5，5.5，1.5，3.5，5.5]|
 
## <a name="next-steps"></a>後續步驟

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)
