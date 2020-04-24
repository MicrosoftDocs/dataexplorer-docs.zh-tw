---
title: unixtime_seconds_todatetime() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 unixtime_seconds_todatetime()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 74abf66dabb7a8fc74085c695081d6a11bfdf3a3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505214"
---
# <a name="unixtime_seconds_todatetime"></a>unixtime_seconds_todatetime()

將 unix 週期秒轉換為 UTC 日期時間。

**語法**

`unixtime_seconds_todatetime(*seconds*)`

**引數**

* *秒*:實數以秒為單位表示紀元時間戳。 `Datetime`在紀元時間之前發生的 (1970-01-01 00:00:00) 具有負時間戳值。

**傳回**

如果轉換成功,則結果將是[日期時間](./scalar-data-types/datetime.md)值。 如果轉換不成功,結果將為空。

**另請參閱**

* 使用[unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)將 unix-紀元毫秒轉換為 UTC 日期時間。
* 使用[unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)將 unix 週期微秒轉換為 UTC 日期時間。
* 使用[unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)將 unix 週期納秒轉換為 UTC 日期時間。

**範例**

```kusto
print date_time = unixtime_seconds_todatetime(1546300800)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|