---
title: 'datetime_part ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 datetime_part ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: c786f0edc94a9b92ca0f4484d0d71166ee699883
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803976"
---
# <a name="datetime_part"></a>datetime_part()

將要求的日期部分解壓縮成整數值。

```kusto
datetime_part("Day",datetime(2015-12-14))
```

## <a name="syntax"></a>語法

`datetime_part(`*部分* `,`*datetime*`)`

## <a name="arguments"></a>引數

* `date`: `datetime`
* `part`: `string`

可能的值 `part` ： 
* 年
* 季
* 月
* week_of_year
* 天
* DayOfYear
* Hour
* 分鐘
* Second
* Millisecond
* 微秒
* 100奈秒

## <a name="returns"></a>傳回

表示已解壓縮之元件的整數。

> [!NOTE]
> `week_of_year`傳回代表周數的整數。 周數是由一年的第一周計算，也就是包含第一個星期四的一個星期。

## <a name="examples"></a>範例

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
> `weekofyear`是元件的過時變體 `week_of_year` 。 `weekofyear`不符合 ISO 8601 規範;一年的第一周定義為一周，其中年份的第一個星期三。
> `week_of_year`符合 ISO 8601 規範;一年的第一周定義為一周，其中的第一個星期四。 [如需詳細資訊](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)。