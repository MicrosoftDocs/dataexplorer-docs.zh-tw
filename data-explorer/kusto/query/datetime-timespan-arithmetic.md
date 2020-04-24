---
title: 日期時間/時間跨度算術 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的日期時間/時間跨度算術。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 34bda7b8418dd519995f25c625a5031202a64a8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516315"
---
# <a name="datetime--timespan-arithmetic"></a>日期時間/時間跨度算術

Kusto 支援對`datetime`類型 值執行算術`timespan`運算 ,並且 :

* 可以減去(但不能添加)兩`datetime`個`timespan`值,以獲得表示其差異的值。
  例如,`datetime(1997-06-25) - datetime(1910-06-11)`[雅克-伊夫·庫斯托](https://en.wikipedia.org/wiki/Jacques_Cousteau)去世時多大了。

* 可以添加或減去兩`timespan`個值以獲得一`timespan`個 值,即其總和或差值。
  例如,`1d + 2d`是三天。

* 可以從`timespan``datetime`值中添加或減去值。
  例如,`datetime(1910-06-11) + 1d`是庫斯托一天變老的日期。

* 可以劃分兩`timespan`個值以獲得其商。
  例如,`1d / 5h``4.8`給出 。
  這使人能夠將任何`timespan`值表示為另一`timespan`個值的倍數。 例如,要以秒為單位表示一小時,只需除`1h``1s``1h / 1s`以 : (`3600`具有明顯的結果 , 。

* 相反`double`,可以`long``timespan`按 值對數值(如和 ) 進行多個`timespan`數位值(如 和), 以獲取值。
  例如,可以表示一個半小時作為`1.5 * 1h`。

## <a name="example-unix-time"></a>範例:Unix 時間

[Unix 時間](https://en.wikipedia.org/wiki/Unix_time)(也稱為 POSIX 時間或 UNIX Epoch 時間)是一個系統,用於將時間點描述為自 1970 年 1 月 1 日星期四 00:00:00 以來經過的秒數減去閏秒。

如果資料包含 Unix 時間表示為整數,或者您需要轉換為它,則以下功能可用:

```kusto
let fromUnixTime = (t:long)
{ 
    datetime(1970-01-01) + t * 1sec 
};
print result = fromUnixTime(1546897531)
```

|result                     |
|---------------------------|
|2019-01-07 21:45:31.0000000|

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
> 除了上述函數之外,請參閱用於 unix 時代轉換的專用函數[:unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)
> [unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)
> [unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)
> [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)