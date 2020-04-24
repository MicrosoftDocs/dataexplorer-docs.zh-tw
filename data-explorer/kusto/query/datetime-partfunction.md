---
title: datetime_part() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的datetime_part()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: c64208725f0d5c49a7ea7733f8eb5a208e19225b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516366"
---
# <a name="datetime_part"></a>datetime_part()

將請求的日期部分提取為整數值。

```kusto
datetime_part("Day",datetime(2015-12-14))
```

**語法**

`datetime_part(`*部份*`,`*日期時間*`)`

**引數**

* `date`: `datetime`
* `part`: `string`

的可能值`part`: 
- Year
- Quarter
- Month
- week_of_year
- Day
- DayOfYear
- Hour
- Minute
- Second
- Millisecond
- 微秒
- 奈秒

**傳回**

表示提取的零件的整數。

**注意**

`week_of_year`返回表示周數的整數。 周數從一年的第一周計算,即包含第一個星期四的周數。

**範例**

```kusto
let dt = datetime(2017-10-30 01:02:03.7654321); 
print 
year = datetime_part("year", dt),
quarter = datetime_part("quarter", dt),
month = datetime_part("month", dt),
weekOfYear = datetime_part("week_of_year", dt),
day = datetime_part("day", dt),
dayOfYear = datetime_part("dayOfYear", dt),
hour = datetime_part("hour", dt),
minute = datetime_part("minute", dt),
second = datetime_part("second", dt),
millisecond = datetime_part("millisecond", dt),
microsecond = datetime_part("microsecond", dt),
nanosecond = datetime_part("nanosecond", dt)

```

|year|quarter|月|weekOfYear|day|dayOfYear|hour|minute|second|毫秒|微秒|奈秒|
|---|---|---|---|---|---|---|---|---|---|---|---|
|2017|4|10|44|30|303|1|2|3|765|765432|765432100|

> [!NOTE]
> `weekofyear`是零件的`week_of_year`過時變體。 `weekofyear`不符合 ISO 8601 標準;一年的第一周被定義為一年的第一個星期。
`week_of_year`符合 ISO 8601 標準;一年的第一周被定義為一年的第一個星期。 [有關詳細資訊](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)。