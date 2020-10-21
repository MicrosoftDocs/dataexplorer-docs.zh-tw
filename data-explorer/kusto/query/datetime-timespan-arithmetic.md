---
title: Datetime/timespan 算術-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Datetime/timespan 算術。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 0b70cb1730d893e07790ade3079be21da209648c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247677"
---
# <a name="datetime--timespan-arithmetic"></a>Datetime/timespan 算術

Kusto 支援對類型的值執行算數運算 `datetime` ，以及 `timespan` ：

* 其中一個可以減去 (但不會新增) 兩個 `datetime` 值，以取得 `timespan` 表示其差異的值。
  例如， `datetime(1997-06-25) - datetime(1910-06-11)` 在壞掉時，是 [Jacques 的舊 Yves Cousteau](https://en.wikipedia.org/wiki/Jacques_Cousteau) 。

* 您可以新增或減去兩個 `timespan` 值，以取得值，也 `timespan` 就是其總和或差異。
  例如， `1d + 2d` 是三天。

* 您可以在值中加入或減去 `timespan` 值 `datetime` 。
  例如， `datetime(1910-06-11) + 1d` 這是一天舊的日期 Cousteau。

* 您可以將兩個 `timespan` 值相除以取得其商。
  例如， `1d / 5h` 提供 `4.8` 。
  如此一來，就可以將任何 `timespan` 值表示為另一個值的倍數 `timespan` 。 例如，若要以秒為單位來表示小時，只要除以 `1h` `1s` ： `1h / 1s` (與明顯的結果， `3600`) 。

* 相反地，您可以有多個數值 (例如 `double` ，並 `long`) `timespan` 值來取得 `timespan` 值。
  例如，您可以將一小時和一半表示為 `1.5 * 1h` 。

## <a name="example-unix-time"></a>範例： Unix 時間

[Unix 時間](https://en.wikipedia.org/wiki/Unix_time) (也稱為 POSIX 時間或 Unix Epoch 時間) 是一種系統，用來描述從00:00:00 星期四、1月1970、國際標準時間 (UTC) 、減去閏秒以來經過的秒數。

如果您的資料包含以整數表示的 Unix 時間，或是您需要轉換為該資料，則可以使用下列功能：

```kusto
let fromUnixTime = (t:long)
{ 
    datetime(1970-01-01) + t * 1sec 
};
print result = fromUnixTime(1546897531)
```

|result                     |
|---------------------------|
|2019-01-07 21：45：31.0000000|

```kusto
let toUnixTime = (dt:datetime) 
{ 
    (dt - datetime(1970-01-01)) / 1s 
};
print result = toUnixTime(datetime(2019-01-07 21:45:31.0000000))
```

|result                     |
|---------------------------|
|1546897531                 |

> [!NOTE]
> 除了上述函數以外，請參閱 unix 的專用函式-epoch 時間轉換： [unixtime_seconds_todatetime ( # B1](unixtime-seconds-todatetimefunction.md) 
>  [unixtime_milliseconds_todatetime ( # B3](unixtime-milliseconds-todatetimefunction.md) 
>  [unixtime_microseconds_todatetime ( # B5](unixtime-microseconds-todatetimefunction.md) 
>  [unixtime_nanoseconds_todatetime ( # B7](unixtime-nanoseconds-todatetimefunction.md)