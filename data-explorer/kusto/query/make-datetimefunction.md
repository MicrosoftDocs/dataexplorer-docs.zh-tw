---
title: make_datetime() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的make_datetime()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c51b99e4d668ec4a5f96cdfd72442d7bcab553a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512915"
---
# <a name="make_datetime"></a>make_datetime()

從指定的日期和時間創建[日期時間](./scalar-data-types/datetime.md)標量值。

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

**語法**

`make_datetime(`*年*,*月*,*日*`)`

`make_datetime(`*年*,*月*,*天*,*小時*,*分鐘*`)`

`make_datetime(`*年*,*月*,*天*,*小時*,*分鐘*,*秒*`)`

**引數**

* *年份*: 年份 (整數值,從 0 到 9999)
* *月*: 月 (整數值, 從 1 到 12 )
* *日*: 天 (整數值, 從 1 到 28-31)
* *小時*:小時(整數值,從 0 到 23)
* *分鐘*:分鐘(整數值,從 0 到 59)
* *第*二個(實際值,從 0 到 59.999999)

**傳回**

如果創建成功,則結果將是[日期時間](./scalar-data-types/datetime.md)值,否則結果將為空。
 
**範例**

```kusto
print year_month_day = make_datetime(2017,10,01)
```

|year_month_day|
|---|
|2017-10-01 00:00:00.0000000|




```kusto
print year_month_day_hour_minute = make_datetime(2017,10,01,12,10)
```

|year_month_day_hour_minute|
|---|
|2017-10-01 12:10:00.0000000|




```kusto
print year_month_day_hour_minute_second = make_datetime(2017,10,01,12,11,0.1234567)
```

|year_month_day_hour_minute_second|
|---|
|2017-10-01 12:11:00.1234567|

