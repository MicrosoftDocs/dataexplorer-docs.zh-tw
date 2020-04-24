---
title: rolling_percentile外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的rolling_percentile外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 02def4069c83eeec080ca059493132619fce30d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510263"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentile外掛程式

返回每個*BinSize*的滾動(滑動 *)BinPerWindow*大小視窗中*指定百*分位數的估計值。

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

**語法**

*T*`| evaluate`T`,`*BinsPerWindow*`,`*ValueColumn*`rolling_percentile(`值*Percentile*柱百分位數索引柱箱大小箱 Per 視窗 = dim1 dim2 ...]`,``,``,``,`*dim2*`,`*IndexColumn**BinSize**dim1*`)`

**引數**

* *T*: 輸入表格表示式。
* *值列*:具有要計算 百分位數的值的列的名稱。 
* *百分位數*:用百分位數計算的Scalar。
* *索引列*:要運行滾動視窗的欄的名稱。
* *BinSize*: 具有要在*索引柱*上應用的條柱大小的 Scalar。
* *BinperWindow*: 每個視窗中包含多個條柱數的 Scalar。
* *dim1,dim2*,...(可選)要切片的維度列清單*dim2*。

**傳回**

返回每個條柱具有行的表(如果指定,則維度組合),該表在視窗中具有以條柱結尾的值的滾動百分位數(包括)。 不同的計數值、新值的不同計數、每個時間視窗的聚合不同計數。

輸出表架構為:


|索引欄|昏暗1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


**範例**

### <a name="rolling-3-day-median-value-per-day"></a>滾動 3 天中位數每天 

下一個查詢計算每日粒度中的 3 天中值。 輸出中的每一行表示最後 3 個條柱(天)的中值,包括 bin 本身。

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
|2018-01-01 00:00:00.0000000|   12|
|2018-01-02 00:00:00.0000000|   24|
|2018-01-03 00:00:00.0000000|   36|
|2018-01-04 00:00:00.0000000|   60|
|2018-01-05 00:00:00.0000000|   84|
|2018-01-06 00:00:00.0000000|   108|
|2018-01-07 00:00:00.0000000|   132|
|2018-01-08 00:00:00.0000000|   156|
|2018-01-09 00:00:00.0000000|   180|
|2018-01-10 00:00:00.0000000|   204|

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>按維度滾動每天 3 天中值

上面相同的示例,但現在還可以計算為維度的每個值分區的滾動視窗。

```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3, EvenOrOdd)
```

|時間戳記| 甚至奧莫德|  rolling_3_percentile_val_50|
|---|---|---|
|2018-01-01 00:00:00.0000000|   甚至|   12|
|2018-01-02 00:00:00.0000000|   甚至|   24|
|2018-01-03 00:00:00.0000000|   甚至|   36|
|2018-01-04 00:00:00.0000000|   甚至|   60|
|2018-01-05 00:00:00.0000000|   甚至|   84|
|2018-01-06 00:00:00.0000000|   甚至|   108|
|2018-01-07 00:00:00.0000000|   甚至|   132|
|2018-01-08 00:00:00.0000000|   甚至|   156|
|2018-01-09 00:00:00.0000000|   甚至|   180|
|2018-01-10 00:00:00.0000000|   甚至|   204|
|2018-01-01 00:00:00.0000000|   奇怪|    11|
|2018-01-02 00:00:00.0000000|   奇怪|    23|
|2018-01-03 00:00:00.0000000|   奇怪|    35|
|2018-01-04 00:00:00.0000000|   奇怪|    59|
|2018-01-05 00:00:00.0000000|   奇怪|    83|
|2018-01-06 00:00:00.0000000|   奇怪|    107|
|2018-01-07 00:00:00.0000000|   奇怪|    131|
|2018-01-08 00:00:00.0000000|   奇怪|    155|
|2018-01-09 00:00:00.0000000|   奇怪|    179|
|2018-01-10 00:00:00.0000000|   奇怪|    203|