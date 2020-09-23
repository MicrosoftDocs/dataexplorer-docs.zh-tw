---
title: 'unixtime_milliseconds_todatetime ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 unixtime_milliseconds_todatetime。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: d30190f320cd728de3cfd851f763954e58abf65f
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103487"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

將 unix-epoch 毫秒轉換成 UTC 日期時間。

## <a name="syntax"></a>語法

`unixtime_milliseconds_todatetime(*milliseconds*)`

## <a name="arguments"></a>引數

* *毫秒*：實數表示 epoch 時間戳記（以毫秒為單位）。 `Datetime` 這發生在 epoch 時間之前 (1970-01-01 00:00:00) 具有負的時間戳記值。

## <a name="returns"></a>傳回

如果轉換成功，結果將會是 [日期時間](./scalar-data-types/datetime.md) 值。 如果轉換不成功，結果將會是 null。

## <a name="see-also"></a>另請參閱

* 使用 [unixtime_seconds_todatetime ( # B1 ](unixtime-seconds-todatetimefunction.md)將 unix-epoch 秒轉換為 UTC 日期時間。
* 使用 [unixtime_microseconds_todatetime ( # B1 ](unixtime-microseconds-todatetimefunction.md)，將 unix-epoch 微秒轉換為 UTC 日期時間。
* 使用 [unixtime_nanoseconds_todatetime ( # B1 ](unixtime-nanoseconds-todatetimefunction.md)將 unix-epoch 毫微秒轉換成 UTC 日期時間。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00：00：00.0000000|
