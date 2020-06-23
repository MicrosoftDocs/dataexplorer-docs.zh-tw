---
title: rolling_percentile 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 rolling_percentile 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ff4ad4adbae580e34c946eb9d18ca3337d3c49c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128881"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentile （）外掛程式

針對每個*BinSize*的輪流（滑動） *BinsPerWindow*大小視窗，傳回*ValueColumn*擴展的指定百分位數估計。

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

**語法**

*T* `| evaluate` `rolling_percentile(` *ValueColumn* `,` *百分位數* `,` *IndexColumn* `,` *BinSize* `,` *BinsPerWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

**引數**

* *T*：輸入表格式運算式。
* *ValueColumn*：資料行的名稱，其中包含要計算百分位數的值。 
* *百分位數*：純量，具有要計算的百分位數。
* *IndexColumn*：要執行滾動視窗的資料行名稱。
* *BinSize*：純量，包含要在*IndexColumn*上套用的 bin 大小。
* *BinsPerWindow*：具有每個視窗中包含之 bin 數目的純量。
* *dim1*， *dim2*，...：（選擇性）要做為配量依據的維度資料行清單。

**傳回**

傳回一個資料表，其中包含每個每個 bin 的資料列（如果有指定，則為維度的組合），其在視窗中的值是以在 bin 結尾（含）的滾動百分位數。 相異計數值、新值的相異計數、每個時間範圍的匯總相異計數。

輸出資料表架構為：


|IndexColumn|dim1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


**範例**

### <a name="rolling-3-day-median-value-per-day"></a>每日輪流推出的3天中間值 

下一個查詢會計算每日資料細微性的3天中間值。 輸出中的每個資料列都代表最後3個 bin （天）的中間值，包括 bin 本身。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3)
```

|時間戳記|rolling_3_percentile_val_50|
|---|---|
|2018-01-01 00：00：00.0000000|   12|
|2018-01-02 00：00：00.0000000|   24|
|2018-01-03 00：00：00.0000000|   36|
|2018-01-04 00：00：00.0000000|   60|
|2018-01-05 00：00：00.0000000|   84|
|2018-01-06 00：00：00.0000000|   108|
|2018-01-07 00：00：00.0000000|   132|
|2018-01-08 00：00：00.0000000|   156|
|2018-01-09 00：00：00.0000000|   180|
|2018-01-10 00：00：00.0000000|   204|

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>依維度每日滾動3天中間值

上述範例與上述相同，但現在也會針對維度的每個值計算已分割的滾動視窗。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3, EvenOrOdd)
```

|時間戳記| EvenOrOdd|  rolling_3_percentile_val_50|
|---|---|---|
|2018-01-01 00：00：00.0000000|   仍|   12|
|2018-01-02 00：00：00.0000000|   仍|   24|
|2018-01-03 00：00：00.0000000|   仍|   36|
|2018-01-04 00：00：00.0000000|   仍|   60|
|2018-01-05 00：00：00.0000000|   仍|   84|
|2018-01-06 00：00：00.0000000|   仍|   108|
|2018-01-07 00：00：00.0000000|   仍|   132|
|2018-01-08 00：00：00.0000000|   仍|   156|
|2018-01-09 00：00：00.0000000|   仍|   180|
|2018-01-10 00：00：00.0000000|   仍|   204|
|2018-01-01 00：00：00.0000000|   正常|    11|
|2018-01-02 00：00：00.0000000|   正常|    23|
|2018-01-03 00：00：00.0000000|   正常|    35|
|2018-01-04 00：00：00.0000000|   正常|    59|
|2018-01-05 00：00：00.0000000|   正常|    83|
|2018-01-06 00：00：00.0000000|   正常|    107|
|2018-01-07 00：00：00.0000000|   正常|    131|
|2018-01-08 00：00：00.0000000|   正常|    155|
|2018-01-09 00：00：00.0000000|   正常|    179|
|2018-01-10 00：00：00.0000000|   正常|    203|
