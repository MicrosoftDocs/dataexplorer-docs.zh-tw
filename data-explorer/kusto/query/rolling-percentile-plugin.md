---
title: rolling_percentile 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 rolling_percentile 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f64a7e5c183e34e81781986d5c28f189a04291e7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242883"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentile ( # A1 外掛程式

傳回 *ValueColumn* 擴展之指定百分位數的估計值，在滾動 (滑動) [ *BinsPerWindow* 大小] 視窗（每個 *BinSize*）。

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `rolling_percentile(` *ValueColumn* `,` *百分位數* `,` *IndexColumn* `,` *BinSize* `,` *BinsPerWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *ValueColumn*：具有值的資料行名稱，用來計算百分位數。 
* *百分*位數：包含要計算之百分位數的純量。
* *IndexColumn*：要執行滾動視窗的資料行名稱。
* *BinSize*：純量（具有要套用至 *IndexColumn*的 bin 大小）。
* *BinsPerWindow*：包含每個視窗中包含之 bin 數目的純量值。
* *dim1*， *dim2*，...： () 選用的維度資料行清單來進行配量。

## <a name="returns"></a>傳回

傳回資料表，其中每個 bin (的資料列，以及維度的組合（如果有指定的) ，且該視窗中的值的滾動百分位數 (包含) 的結尾。 相異計數值、新值的相異計數、每個時間範圍的匯總相異計數。

輸出資料表架構為：


|IndexColumn|dim1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


## <a name="examples"></a>範例

### <a name="rolling-3-day-median-value-per-day"></a>每天滾動3天的中間值 

下一個查詢會在每日的資料細微性中計算3天的中間值。 輸出中的每個資料列都代表最後3個 bin (天) 的中間值，包括 bin 本身。

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

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>依維度的每日滾動3天中間值

上述的相同範例，但現在也會針對維度的每個值計算分割的滾動視窗。

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
|2018-01-01 00：00：00.0000000|   甚至|   12|
|2018-01-02 00：00：00.0000000|   甚至|   24|
|2018-01-03 00：00：00.0000000|   甚至|   36|
|2018-01-04 00：00：00.0000000|   甚至|   60|
|2018-01-05 00：00：00.0000000|   甚至|   84|
|2018-01-06 00：00：00.0000000|   甚至|   108|
|2018-01-07 00：00：00.0000000|   甚至|   132|
|2018-01-08 00：00：00.0000000|   甚至|   156|
|2018-01-09 00：00：00.0000000|   甚至|   180|
|2018-01-10 00：00：00.0000000|   甚至|   204|
|2018-01-01 00：00：00.0000000|   奇怪|    11|
|2018-01-02 00：00：00.0000000|   奇怪|    23|
|2018-01-03 00：00：00.0000000|   奇怪|    35|
|2018-01-04 00：00：00.0000000|   奇怪|    59|
|2018-01-05 00：00：00.0000000|   奇怪|    83|
|2018-01-06 00：00：00.0000000|   奇怪|    107|
|2018-01-07 00：00：00.0000000|   奇怪|    131|
|2018-01-08 00：00：00.0000000|   奇怪|    155|
|2018-01-09 00：00：00.0000000|   奇怪|    179|
|2018-01-10 00：00：00.0000000|   奇怪|    203|
