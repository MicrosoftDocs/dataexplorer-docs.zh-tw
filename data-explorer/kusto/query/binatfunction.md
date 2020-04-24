---
title: bin_at() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 bin_at()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 218142e1c377e6a72abde154d4576025698b2f0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517437"
---
# <a name="bin_at"></a>bin_at()

將值舍入到固定大小的"bin",並控制 bin 的起點。
( 另請參[`bin function`](./binfunction.md)閱 。

**語法**

`bin_at``(``,`*BinSize*`, `運算式 BinSize*固定點**Expression*`)`

**引數**

* *運算式*: 表示要捨入的值的數字類型`datetime`的`timespan`標 量 運算式(包括和)。
* *BinSize*: 與表示每個條柱大小的*運算式*相同的標量常數。 
* *固定點*:與*運算式*相同的標量常量,表示*運算式*的一個值,即"固定`fixed_point``bin_at(fixed_point, bin_size, fixed_point) == fixed_point`點"(即 .) 的值。

**傳回**

在*運算式*下方的*BinSize*最近的倍數移動,以便*固定點*將轉換為自身。

**範例**

|運算是                                                                    |結果                           |註解                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|所有垃圾箱都將在中午   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|所有垃圾箱都將在周日|


在下面的範例中,請注意 arg`"fixed point"`作為 bin 之一返回,其他 bin 根據`bin_size`( 對齊形式) 與其 對齊。 另請注意,每個日期時間 bin 表示該 bin 的開始時間:

```kusto

datatable(Date:datetime, Num:int)[
datetime(2018-02-24T15:14),3,
datetime(2018-02-23T16:14),4,
datetime(2018-02-26T15:14),5]
| summarize sum(Num) by bin_at(Date, 1d, datetime(2018-02-24 15:14:00.0000000)) 
```

|Date|sum_Num|
|---|---|
|2018-02-23 15:14:00.0000000|4|
|2018-02-24 15:14:00.0000000|3|
|2018-02-26 15:14:00.0000000|5|