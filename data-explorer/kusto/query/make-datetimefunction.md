---
title: make_datetime （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 make_datetime （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 589be4fbbc285309e86647ce8d7392840aedea0e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346995"
---
# <a name="make_datetime"></a>make_datetime()

從指定的日期和時間建立[日期時間](./scalar-data-types/datetime.md)純量值。

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

## <a name="syntax"></a>語法

`make_datetime(`*年*、*月*、*日*`)`

`make_datetime(`*年*、*月*、*日*、*小時*、*分鐘*`)`

`make_datetime(`*年*、*月*、*日*、*小時*、*分鐘*、*秒*`)`

## <a name="arguments"></a>引數

* *年*：年（從0到9999的整數值）
* *month*： month （從1到12的整數值）
* *day*： day （從1到28-31 的整數值）
* *小時*：小時（從0到23的整數值）
* *分鐘*：分鐘（從0到59的整數值）
* *第*二個：第二個（實值，從0到59.9999999）

## <a name="returns"></a>傳回

如果建立成功，結果會是[日期時間](./scalar-data-types/datetime.md)值，否則結果會是 null。
 
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

