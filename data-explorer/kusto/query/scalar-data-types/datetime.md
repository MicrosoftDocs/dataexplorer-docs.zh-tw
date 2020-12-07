---
title: 日期時間資料類型 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的日期時間資料類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: e65b16ac4a280f2ecbef2c4557cac4a8d7be13b3
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513126"
---
# <a name="the-datetime-data-type"></a>日期時間資料類型

`datetime` (`date`) 資料類型表示時間的瞬間，通常以一天的日期和時間表示。
值的範圍是從西元 0001 年 1 月 1 日午夜 00:00:00 到西元 9999 年 12 月 31 日 11:59:59 P.M. 。 

時間值會以 100 奈秒為單位 (稱為刻度) 來測量，而特定日期的刻度數目是從西元 0001 年 1 月 1 日午夜 12:00 算起 (不包括因為閏秒而增加的刻度)。
例如，刻度值 31241376000000000 代表的日期是 0100 年 1 月 1 日星期五午夜 12:00:00。
這有時稱為「線性時間點」。

> [!WARNING]
> Kusto 中的 `datetime` 值一律為 UTC 時區。 顯示其他時區中的 `datetime` 值應由顯示資料的使用者應用程式負責，而不是資料本身的屬性。 如果需要將時區值保存為資料的一部分，則應該使用個別的資料行 (提供相對於 UTC 的時差資訊)。

## <a name="datetime-literals"></a>datetime 常值

`datetime`類型的常值具有 `datetime(`*value*`)` 語法，其中 value支援數種格式，如下表所示：

|範例                                                     |值                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|時間一律是 UTC 格式。 省略日期則會提供今天的時間。|
|`datetime(null)`                                            |請參閱 [Null 值](null-values.md)。                            |
|`now()`                                                     |目前的時間。                                             |
|`now(`-*timespan*`)`                                        |`now()-`*timespan*                                            |
|`ago(`*timespan*`)`                                         |`now()-`*timespan*                                            |

`now()` 和 `ago()` 表示 `datetime` 值 (與 Kusto 開始執行查詢時的時間點相比)。 這些語法可能會在同一個查詢中出現多次，而且所有的語法都會用同一個值。
(換句話說，`now(-x) - ago(x)` 之類的運算式一律會評估為其值為零的 `timespan`)。

## <a name="supported-formats"></a>支援的格式

`datetime` 有數種格式，皆可支援作為 [datetime() 常值](#datetime-literals) 和 [todatetime()](../todatetimefunction.md) 函式。

> [!WARNING]
> **強烈建議您** 只使用 ISO 8601 格式。

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|[格式]|範例|
|------|-------|
|%Y-%m-%dT%H:%M:%s%z|2014-05-25T08:20:03.123456Z|
|%Y-%m-%dT%H:%M:%s|2014-05-25T08:20:03.123456|
|%Y-%m-%dT%H:%M|2014-05-25T08:20|
|%Y-%m-%d %H:%M:%s%z|2014-11-08 15:55:55.123456Z|
|%Y-%m-%d %H:%M:%s|2014-11-08 15:55:55|
|%Y-%m-%d %H:%M|2014-11-08 15:55|
|%Y-%m-%d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|[格式]|範例|
|------|-------|
|%w, %e %b %r %H:%M:%s %Z|Sat, 8 Nov 14 15:05:02 GMT|
|%w, %e %b %r %H:%M:%s|Sat, 8 Nov 14 15:05:02|
|%w, %e %b %r %H:%M|Sat, 8 Nov 14 15:05|
|%w, %e %b %r %H:%M %Z|Sat, 8 Nov 14 15:05 GMT|
|%e %b %r %H:%M:%s %Z|8 Nov 14 15:05:02 GMT|
|%e %b %r %H:%M:%s|8 Nov 14 15:05:02|
|%e %b %r %H:%M|8 Nov 14 15:05|
|%e %b %r %H:%M %Z|8 Nov 14 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|[格式]|範例|
|------|-------|
|%w, %e-%b-%r %H:%M:%s %Z|Saturday, 08-Nov-14 15:05:02 GMT|
|%w, %e-%b-%r %H:%M:%s|Saturday, 08-Nov-14 15:05:02|
|%w, %e-%b-%r %H:%M %Z|Saturday, 08-Nov-14 15:05 GMT|
|%w, %e-%b-%r %H:%M|Saturday, 08-Nov-14 15:05|
|%e-%b-%r %H:%M:%s %Z|08-Nov-14 15:05:02 GMT|
|%e-%b-%r %H:%M:%s|08-Nov-14 15:05:02|
|%e-%b-%r %H:%M %Z|08-Nov-14 15:05 GMT|
|%e-%b-%r %H:%M|08-Nov-14 15:05|


### <a name="sortable"></a>可分類 

|[格式]|範例|
|------|-------|        
|%Y-%n-%e %H:%M:%s|2014-11-08 15:05:25|
|%Y-%n-%e %H:%M:%s %Z|2014-11-08 15:05:25 GMT|
|%Y-%n-%e %H:%M|2014-11-08 15:05|
|%Y-%n-%e %H:%M %Z|2014-11-08 15:05 GMT|
|%Y-%n-%eT%H:%M:%s|2014-11-08T15:05:25|
|%Y-%n-%eT%H:%M:%s %Z|2014-11-08T15:05:25 GMT|
|%Y-%n-%eT%H:%M|2014-11-08T15:05|
|%Y-%n-%eT%H:%M %Z|2014-11-08T15:05 GMT|
