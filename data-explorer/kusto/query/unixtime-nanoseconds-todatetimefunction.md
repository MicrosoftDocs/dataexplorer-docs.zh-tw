---
title: unixtime_nanoseconds_todatetime （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 unixtime_nanoseconds_todatetime （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 0786863589ece863278f9cadec79c9c86a4117eb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350599"
---
# <a name="unixtime_nanoseconds_todatetime"></a>unixtime_nanoseconds_todatetime()

將 unix-epoch 毫微秒轉換成 UTC 日期時間。

## <a name="syntax"></a>語法

`unixtime_nanoseconds_todatetime(*nanoseconds*)`

## <a name="arguments"></a>引數

* *毫微秒*：實際數位代表 epoch 時間戳記（以毫微秒為單位）。 `Datetime`在 epoch 時間（1970-01-01 00:00:00）之前發生的情況，其時間戳記值為負值。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是[日期時間](./scalar-data-types/datetime.md)值。 如果轉換不成功，則結果會是 null。

**另請參閱**

* 使用[unixtime_seconds_todatetime （）](unixtime-seconds-todatetimefunction.md)，將 unix-epoch 秒轉換成 UTC 日期時間。
* 使用[unixtime_milliseconds_todatetime （）](unixtime-milliseconds-todatetimefunction.md)，將 unix epoch 毫秒轉換成 UTC 日期時間。
* 使用[unixtime_microseconds_todatetime （）](unixtime-microseconds-todatetimefunction.md)，將 unix-epoch 微秒轉換成 UTC 日期時間。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_nanoseconds_todatetime(1546300800000000000)
```

|date_time|
|---|
|2019-01-01 00：00：00.0000000|
