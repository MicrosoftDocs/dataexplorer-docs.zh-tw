---
title: 'datetime_add ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 datetime_add。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ab395dadf178b296929300fe4cfd42742fba5f27
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247730"
---
# <a name="datetime_add"></a>datetime_add()

從指定的 datepart 乘以指定的數量來計算新的 [日期時間](./scalar-data-types/datetime.md) ，並將其加入至指定的 [日期時間](./scalar-data-types/datetime.md)。

## <a name="syntax"></a>語法

`datetime_add(`*期間* `,`*金額* `,`*datetime*`)`

## <a name="arguments"></a>引數

* `period`： [字串](./scalar-data-types/string.md)。 
* `amount`： [整數](./scalar-data-types/int.md)。
* `datetime`： [日期時間](./scalar-data-types/datetime.md) 值。

可能的 *期間*值： 
- 年
- 季
- Month
- 週
- 天
- Hour
- 分鐘
- Second
- Millisecond
- 微秒
- 納 秒

## <a name="returns"></a>傳回

新增特定時間/日期間隔之後的日期。

## <a name="examples"></a>範例

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

|year|quarter|月|week|day|hour|分|秒|
|---|---|---|---|---|---|---|---|
|2018-01-01 00：00：00.0000000|2017-04-01 00：00：00.0000000|2017-02-01 00：00：00.0000000|2017-01-08 00：00：00.0000000|2017-01-02 00：00：00.0000000|2017-01-01 01：00：00.0000000|2017-01-01 00：01：00.0000000|2017-01-01 00：00：01.0000000|






