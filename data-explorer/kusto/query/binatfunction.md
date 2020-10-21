---
title: 'bin_at ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 bin_at。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ae888fc050387af28281b84229044114a72a5dbf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245391"
---
# <a name="bin_at"></a>bin_at()

將值向下舍入為固定大小的「bin」，可控制 bin 的起點。
 (另請參閱 [`bin function`](./binfunction.md) 。 ) 

## <a name="syntax"></a>語法

`bin_at``(` *Expression* `,` *BinSize* `, ` *FixedPoint*`)`

## <a name="arguments"></a>引數

* *運算式*：數值型別的純量運算式 (包括 `datetime` 和 `timespan`) 指出要舍入的值。
* *BinSize*：數數值型別的純量常數或 `timespan` (`datetime` `timespan` *運算式*) 指出每個 bin 的大小。
* *FixedPoint*：與 *運算式* 相同類型的純量常數，表示 *運算式* 的一個值為「固定點」 (也就是值的值 `fixed_point` `bin_at(fixed_point, bin_size, fixed_point) == fixed_point` 。 ) 

## <a name="returns"></a>傳回

*BinSize*下的最接近倍數*運算式*會移位，讓*FixedPoint*轉譯成本身。

## <a name="examples"></a>範例

|運算式                                                                    |結果                           |註解                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|所有的 bin 都將在中午   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|所有的 bin 都會在星期日|


在下列範例中，請注意， `"fixed point"` arg 會以其中一個 bin 的形式傳回，而另一個則會根據來對齊 `bin_size` 。 也請注意，每個 datetime bin 代表該 bin 的開始時間：

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
