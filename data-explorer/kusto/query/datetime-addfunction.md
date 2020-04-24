---
title: datetime_add() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 datetime_add()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ead7e0ae5c4dee94930afe1b20c4d5b99e2b4664
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516451"
---
# <a name="datetime_add"></a>datetime_add()

計算指定日期部份的新[日期時間](./scalar-data-types/datetime.md)乘以指定金額,新增到指定[日期時間](./scalar-data-types/datetime.md)。

**語法**

`datetime_add(`*period*`,`期間*amount*`,`值*日期時間*`)`

**引數**

* `period`:[字串](./scalar-data-types/string.md)。 
* `amount`:[整數](./scalar-data-types/int.md)。
* `datetime`:[日期時間](./scalar-data-types/datetime.md)值。

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

已添加特定時間/日期間隔後的日期。

**範例**

```kusto
print  year = datetime_add('year',1,make_datetime(2017,1,1)),
quarter = datetime_add('quarter',1,make_datetime(2017,1,1)),
month = datetime_add('month',1,make_datetime(2017,1,1)),
week = datetime_add('week',1,make_datetime(2017,1,1)),
day = datetime_add('day',1,make_datetime(2017,1,1)),
hour = datetime_add('hour',1,make_datetime(2017,1,1)),
minute = datetime_add('minute',1,make_datetime(2017,1,1)),
second = datetime_add('second',1,make_datetime(2017,1,1))

```

|year|quarter|月|week|day|hour|minute|second|
|---|---|---|---|---|---|---|---|
|2018-01-01 00:00:00.0000000|2017-04-01 00:00:00.0000000|2017-02-01 00:00:00.0000000|2017-01-08 00:00:00.0000000|2017-01-02 00:00:00.0000000|2017-01-01 01:00:00.0000000|2017-01-01 00:01:00.0000000|2017-01-01 00:00:01.0000000|






