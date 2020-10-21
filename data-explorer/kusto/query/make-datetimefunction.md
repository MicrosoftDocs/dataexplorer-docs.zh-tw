---
title: 'make_datetime ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 make_datetime。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 09a199e907f8e09a845fa037129330a2b660187e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249833"
---
# <a name="make_datetime"></a>make_datetime()

從指定的日期和時間建立 [datetime](./scalar-data-types/datetime.md) 純量值。

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

## <a name="syntax"></a>語法

`make_datetime(`*年*、*月*、*日*`)`

`make_datetime(`*year*、*month*、*day*、*hour*、*minute*`)`

`make_datetime(`*year*、*month*、*day*、*hour*、*minute*、*second*`)`

## <a name="arguments"></a>引數

* *year*： year (整數值，從0到 9999) 
* *month*： month (integer 值，從1到 12) 
* *day*： day (整數值，從1到 28-31) 
* *小時*：小時 (整數值，從0到 23) 
* *分鐘*：分鐘 (整數值，從0到 59) 
* *第二個*：第二個 (實值，從0到 59.9999999) 

## <a name="returns"></a>傳回

如果建立成功，結果將會是 [日期時間](./scalar-data-types/datetime.md) 值，否則結果會是 null。
 
## <a name="example"></a>範例

```kusto
print year_month_day = make_datetime(2017,10,01)
```

|year_month_day|
|---|
|2017-10-01 00：00：00.0000000|




```kusto
print year_month_day_hour_minute = make_datetime(2017,10,01,12,10)
```

|year_month_day_hour_minute|
|---|
|2017-10-01 12：10：00.0000000|




```kusto
print year_month_day_hour_minute_second = make_datetime(2017,10,01,12,11,0.1234567)
```

|year_month_day_hour_minute_second|
|---|
|2017-10-01 12：11：00.1234567|

