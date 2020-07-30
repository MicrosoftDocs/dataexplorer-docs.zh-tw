---
title: datetime_add （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 datetime_add （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 766f0617b70e21194d731ae1cf8eabf1014265bb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348542"
---
# <a name="datetime_add"></a>datetime_add()

從指定的 datepart 乘以指定的數量來計算新的[日期時間](./scalar-data-types/datetime.md)，並加入至指定的[日期時間](./scalar-data-types/datetime.md)。

## <a name="syntax"></a>語法

`datetime_add(`*期間* `,`*金額* `,`*datetime*`)`

## <a name="arguments"></a>引數

* `period`：[字串](./scalar-data-types/string.md)。 
* `amount`：[整數](./scalar-data-types/int.md)。
* `datetime`：[日期時間](./scalar-data-types/datetime.md)值。

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

加入特定時間/日期間隔之後的日期。

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

|year|quarter|月|week|day|hour|minute|second|
|---|---|---|---|---|---|---|---|
|2018-01-01 00：00：00.0000000|2017-04-01 00：00：00.0000000|2017-02-01 00：00：00.0000000|2017-01-08 00：00：00.0000000|2017-01-02 00：00：00.0000000|2017-01-01 01：00：00.0000000|2017-01-01 00：01：00.0000000|2017-01-01 00：00：01.0000000|






