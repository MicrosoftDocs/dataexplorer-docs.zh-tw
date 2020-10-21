---
title: 'unixtime_microseconds_todatetime ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 unixtime_microseconds_todatetime。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 713cf936878d0fa311d4637f61881f0481229617
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245700"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

將 unix-epoch 微秒轉換成 UTC 日期時間。

## <a name="syntax"></a>語法

`unixtime_microseconds_todatetime(*microseconds*)`

## <a name="arguments"></a>引數

* *微秒*：實數表示 epoch 時間戳記（以微秒為單位）。 `Datetime` 這發生在 epoch 時間之前 (1970-01-01 00:00:00) 具有負的時間戳記值。

## <a name="returns"></a>傳回

如果轉換成功，結果將會是 [日期時間](./scalar-data-types/datetime.md) 值。 如果轉換不成功，結果將會是 null。

## <a name="see-also"></a>另請參閱

* 使用 [unixtime_seconds_todatetime ( # B1 ](unixtime-seconds-todatetimefunction.md)將 unix-epoch 秒轉換為 UTC 日期時間。
* 使用 [unixtime_milliseconds_todatetime ( # B1 ](unixtime-milliseconds-todatetimefunction.md)，將 unix-epoch 毫秒轉換為 UTC 日期時間。
* 使用 [unixtime_nanoseconds_todatetime ( # B1 ](unixtime-nanoseconds-todatetimefunction.md)將 unix-epoch 毫微秒轉換成 UTC 日期時間。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00：00：00.0000000|
