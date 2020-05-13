---
title: unixtime_milliseconds_todatetime （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 unixtime_milliseconds_todatetime （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 26f1eb901798a28996c8cdc148fe68a71fb9d2b8
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370274"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

將 unix epoch 毫秒轉換成 UTC 日期時間。

**語法**

`unixtime_milliseconds_todatetime(*milliseconds*)`

**引數**

* *毫秒*：實際數位代表 epoch 時間戳記（以毫秒為單位）。 `Datetime`在 epoch 時間（1970-01-01 00:00:00）之前發生的情況，其時間戳記值為負值。

**傳回**

如果轉換成功，則結果會是[日期時間](./scalar-data-types/datetime.md)值。 如果轉換不成功，則結果會是 null。

**另請參閱**

* 使用[unixtime_seconds_todatetime （）](unixtime-seconds-todatetimefunction.md)，將 unix-epoch 秒轉換成 UTC 日期時間。
* 使用[unixtime_microseconds_todatetime （）](unixtime-microseconds-todatetimefunction.md)，將 unix-epoch 微秒轉換成 UTC 日期時間。
* 使用[unixtime_nanoseconds_todatetime （）](unixtime-nanoseconds-todatetimefunction.md)，將 unix epoch 毫微秒轉換成 UTC 日期時間。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00：00：00.0000000|
