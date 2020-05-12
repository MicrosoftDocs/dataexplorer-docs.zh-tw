---
title: bin_at （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 bin_at （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 90055f644dbf653eb65546202832f7cab834a0ac
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227584"
---
# <a name="bin_at"></a>bin_at()

將值向下舍入固定大小的「bin」，並控制 bin 的起點。
（另請參閱 [`bin function`](./binfunction.md) ）。

**語法**

`bin_at``(` *Expression* `,` *BinSize* `, ` *FixedPoint*`)`

**引數**

* *Expression*：數數值型別的純量運算式（包括 `datetime` 和 `timespan` ），指出要舍入的值。
* *BinSize*：與*運算式*相同類型的純量常數，表示每個 bin 的大小。 
* *FixedPoint*：與*運算式*相同類型的純量常數，表示*運算式*的一個值，也就是「固定點」（也就是的值 `fixed_point` `bin_at(fixed_point, bin_size, fixed_point) == fixed_point` ）。

**傳回**

*BinSize*下方*運算式*的最接近倍數會移位，使*FixedPoint*會轉譯成本身。

**範例**

|運算是                                                                    |結果                           |評價                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|所有的 bin 皆為中午   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|所有的 bin 皆會在星期日|


在下列範例中，請注意， `"fixed point"` arg 會當做其中一個 bin 傳回，而其他的則會根據來對齊 `bin_size` 。 另請注意，每個日期時間 bin 代表該 bin 的開始時間：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto

datatable(Date:datetime, Num:int)[
datetime(2018-02-24T15:14),3,
datetime(2018-02-23T16:14),4,
datetime(2018-02-26T15:14),5]
| summarize sum(Num) by bin_at(Date, 1d, datetime(2018-02-24 15:14:00.0000000)) 
```

|Date|sum_Num|
|---|---|
|2018-02-23 15：14：00.0000000|4|
|2018-02-24 15：14：00.0000000|3|
|2018-02-26 15：14：00.0000000|5|
