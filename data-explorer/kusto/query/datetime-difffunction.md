---
title: datetime_diff() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的datetime_diff()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fd62e27ac4f9ef0ec813a311ddb2b16f0a6c9a65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516434"
---
# <a name="datetime_diff"></a>datetime_diff()

計算兩個[日期時間](./scalar-data-types/datetime.md)值之間的行事曆差異。

**語法**

`datetime_diff(`*期間*`,`*datetime_1*`,`datetime_1datetime_2* *`)`

**引數**

* `period`: `string`. 
* `datetime_1`:[日期時間](./scalar-data-types/datetime.md)值。
* `datetime_2`:[日期時間](./scalar-data-types/datetime.md)值。

*期間*的可能值 : 
- Year
- Quarter
- Month
- 週
- Day
- Hour
- Minute
- Second
- Millisecond
- 微秒
- 奈秒

**傳回**

一個整數,它表示減法`periods`結果中的量`datetime_1` - `datetime_2`。

**範例**

```kusto
print
year = datetime_diff('year',datetime(2017-01-01),datetime(2000-12-31)),
quarter = datetime_diff('quarter',datetime(2017-07-01),datetime(2017-03-30)),
month = datetime_diff('month',datetime(2017-01-01),datetime(2015-12-30)),
week = datetime_diff('week',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
day = datetime_diff('day',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
hour = datetime_diff('hour',datetime(2017-10-31 01:00),datetime(2017-10-30 23:59)),
minute = datetime_diff('minute',datetime(2017-10-30 23:05:01),datetime(2017-10-30 23:00:59)),
second = datetime_diff('second',datetime(2017-10-30 23:00:10.100),datetime(2017-10-30 23:00:00.900)),
millisecond = datetime_diff('millisecond',datetime(2017-10-30 23:00:00.200100),datetime(2017-10-30 23:00:00.100900)),
microsecond = datetime_diff('microsecond',datetime(2017-10-30 23:00:00.1009001),datetime(2017-10-30 23:00:00.1008009)),
nanosecond = datetime_diff('nanosecond',datetime(2017-10-30 23:00:00.0000000),datetime(2017-10-30 23:00:00.0000007))
```

|year|quarter|月|week|day|hour|minute|second|毫秒|微秒|奈秒|
|---|---|---|---|---|---|---|---|---|---|---|
|17|2|13|5|29|2|5|10|100|100|-700|



