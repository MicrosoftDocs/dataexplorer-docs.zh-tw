---
title: 'series_seasonal ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_seasonal。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7997e6312f43316918c197c5ec10eec281495c63
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245930"
---
# <a name="series_seasonal"></a>series_seasonal()

根據偵測到或指定的季節性期間，計算數列的季節性元件。

## <a name="syntax"></a>語法

`series_seasonal(`*系列* `[,`*期間*`])`

## <a name="arguments"></a>引數

* *數列*：輸入數值動態陣列
* *期間* (選擇性) ：每個季節性期間的整數個 bin 數目，可能的值：
    *  -1 (預設) ：使用 [series_periods_detect ( # B3 ](series-periods-detectfunction.md) （臨界值為 *0.7*）自動偵測 dll 句號。 如果未偵測到季節性，則傳回零
    * 正整數：用作季節性元件的期間
    * 任何其他值：忽略季節性並傳回一連串零

## <a name="returns"></a>傳回

與 *數列* 輸入相同長度的動態陣列，包含數列的計算季節性元件。 季節性元件的計算方式為所有值的 *中間* 值（這些值會對應至整個期間的 bin 位置）。

## <a name="examples"></a>範例

### <a name="auto-detect-the-period"></a>自動偵測期間

在下列範例中，系統會自動偵測到數列的句號。 第一個數列的期間偵測到六個儲箱，第二個則是第二個。第三個數列的期間太短而無法偵測，並傳回一系列的零。 請參閱下一個範例， [以瞭解如何強制執行期間](#force-a-period)。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2，5，3，4，3，2，1，2，3，4，3，2，1，2，3，4，3，2，1，2，3，4，3，2，1]|[1.0、2.0、3.0、4.0、3.0、2.0、1.0、2.0、3.0、4.0、3.0、2.0、1.0、2.0、3.0、4.0、3.0、2.0、1.0、2.0、3.0、4.0、3.0、2.0、1.0]|
|[8、12、14、12、10、10、12、14、12、10、10、12、14、12、10、10、12、14、12、10]|[10.0、12.0、14.0、12.0、10.0、10.0、12.0、14.0、12.0、10.0、10.0、12.0、14.0、12.0、10.0、10.0]|
|[1，3，5，2，4，6，1，3，5，2，4，6]|[0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0]|

### <a name="force-a-period"></a>強制執行句號

在此範例中， [series_periods_detect ( # B1 ](series-periods-detectfunction.md)無法偵測到數列的時間太短，所以我們明確地強制該期間取得季節性模式。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1，3，5，1，3，5，2，4，6]|[1.0、3.0、5.0、1.0、3.0、5.0、1.0、3.0、5.0]|
|[1，3，5，2，4，6，1，3，5，2，4，6]|[1.5、3.5、5.5、1.5、3.5、5.5、1.5、3.5、5.5、1.5、3.5、5.5]|
 
## <a name="next-steps"></a>後續步驟

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)
