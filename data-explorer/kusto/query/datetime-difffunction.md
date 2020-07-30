---
title: datetime_diff （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 datetime_diff （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e116661610e343c90276a43421d263bf74cd1b5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348525"
---
# <a name="datetime_diff"></a>datetime_diff()

計算兩個[日期時間](./scalar-data-types/datetime.md)值之間的 calendarian 差異。

## <a name="syntax"></a>語法

`datetime_diff(`*期間* `,`*datetime_1* `,`*datetime_2*`)`

## <a name="arguments"></a>引數

* `period`: `string`. 
* `datetime_1`：[日期時間](./scalar-data-types/datetime.md)值。
* `datetime_2`：[日期時間](./scalar-data-types/datetime.md)值。

*期間*的可能值： 
- 年
- 季
- 月
- 週
- 天
- Hour
- 分鐘
- Second
- Millisecond
- 微秒
- 100奈秒

## <a name="returns"></a>傳回

整數，代表 `periods` 減法（）結果中的數量 `datetime_1`  -  `datetime_2` 。

## <a name="examples"></a>範例

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



