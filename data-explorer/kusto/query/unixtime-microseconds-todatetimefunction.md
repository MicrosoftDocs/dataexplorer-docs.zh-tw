---
title: unixtime_microseconds_todatetime() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的unixtime_microseconds_todatetime()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 6ab593049fcafeba27b8158a1c9c6a5b6fa75fe3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505265"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

將 unix 週期微秒轉換為 UTC 日期時間。

**語法**

`unixtime_microseconds_todatetime(*microseconds*)`

**引數**

* *微秒*:實數以微秒為單位表示紀元時間戳。 `Datetime`在紀元時間之前發生的 (1970-01-01 00:00:00) 具有負時間戳值。

**傳回**

如果轉換成功,則結果將是[日期時間](./scalar-data-types/datetime.md)值。 如果轉換不成功,結果將為空。

**另請參閱**

* 使用[unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)將 unix 週期秒轉換為 UTC 日期時間。
* 使用[unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)將 unix-紀元毫秒轉換為 UTC 日期時間。
* 使用[unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)將 unix 週期納秒轉換為 UTC 日期時間。

**範例**

```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|