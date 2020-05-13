---
title: unixtime_microseconds_todatetime （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 unixtime_microseconds_todatetime （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: f69261e7ac84d8ae77fd1d2fb89f31ede2536443
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370250"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

將 unix-epoch 微秒轉換成 UTC 日期時間。

**語法**

`unixtime_microseconds_todatetime(*microseconds*)`

**引數**

* *微秒*：實數代表 epoch 時間戳記（以毫秒為單位）。 `Datetime`在 epoch 時間（1970-01-01 00:00:00）之前發生的情況，其時間戳記值為負值。

**傳回**

如果轉換成功，則結果會是[日期時間](./scalar-data-types/datetime.md)值。 如果轉換不成功，則結果會是 null。

**另請參閱**

* 使用[unixtime_seconds_todatetime （）](unixtime-seconds-todatetimefunction.md)，將 unix-epoch 秒轉換成 UTC 日期時間。
* 使用[unixtime_milliseconds_todatetime （）](unixtime-milliseconds-todatetimefunction.md)，將 unix epoch 毫秒轉換成 UTC 日期時間。
* 使用[unixtime_nanoseconds_todatetime （）](unixtime-nanoseconds-todatetimefunction.md)，將 unix epoch 毫微秒轉換成 UTC 日期時間。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00：00：00.0000000|
