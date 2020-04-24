---
title: 日期時間資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的日期時間數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5a234c49a5bb5b04ccde49e7fb3702d927e38b5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509906"
---
# <a name="the-datetime-data-type"></a>日期時間資料類型

`datetime` (`date`) 數據類型表示時間的瞬間,通常表示為一天的日期和時間。
值範圍從 00:00:00(午夜),0001 年 1 月 1 日 Anno Domini(共同時代)到 11:59:59 下午,9999 年 12 月 31 日。 (C.E.)在公曆中。 

時間值以稱為刻度的 100 納秒單位進行測量,特定日期是自 10001 年 1 月 1 日午夜 12:00 起的報價。 (C.E.)在公曆日曆中(不包括以閏秒添加的刻度)。
例如,刻度值 3124137600000000000000 表示日期,0100 年 1 月 1 日(星期五)12:00:午夜。
這有時被稱為"線性時間時刻"。

> [!WARNING]
> Kusto`datetime`中 的值始終位於 UTC 時區中。 在其他時區`datetime`中顯示值是顯示數據的使用者應用程式的責任,而不是數據本身的屬性。 如果需要將時區值保留為數據的一部分,則應使用單獨的列(提供相對於 UTC 的偏移資訊)。

## <a name="datetime-literals"></a>datetime 常值

型`datetime`態文字具有語`datetime(`法*值*`)`,其中支援許多*格式的值,* 如下表所示:

|範例                                                     |值                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|時間一律是 UTC 格式。 省略日期則會提供今天的時間。|
|`datetime(null)`                                            |請參考[空數](null-values.md)。                            |
|`now()`                                                     |目前的時間。                                             |
|`now(`-*時間跨度*`)`                                        |`now()-`*時間跨度*                                            |
|`ago(`*時間跨度*`)`                                         |`now()-`*時間跨度*                                            |

`now()`並`ago()`指示與`datetime`Kusto 開始執行查詢的時間之時相比的值。 這些值可以在同一查詢中多次出現,並且將為此使用單個值。
(換句話說,表達式(如`now(-x) - ago(x)`始終計算為`timespan`零 值)。

## <a name="supported-formats"></a>支援的格式

有幾個格式`datetime`支援作為[datetime() 文字](#datetime-literals)和[日期時間()](../todatetimefunction.md)函數。

> [!WARNING]
> **強烈建議僅**使用 ISO 8601 格式。

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|[格式]|範例|
|------|-------|
|%Y-%m-%dT%H:%M:%s%z|2014-05-25T08:20:03.123456Z|
|%Y-%m-%dT%H:%M:%s"|2014-05-25T08:20:03.123456|
|%Y-%m-%dT%H:%M"|2014-05-25T08:20|
|%Y-%m-%d %H:%M:%s%z"|2014-11-08 15:55:55.123456Z|
|%Y-%m-%d %H:%M:%s"|2014-11-08 15:55:55|
|%Y-%m-%d %H:%M"|2014-11-08 15:55|
|%Y年%b%-d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|[格式]|範例|
|------|-------|
|%w, %e %b %r %H:%M:%s %Z|11月8日 星期六 15:05:02 GMT|
|%w, %e %b %r %H:%M:%s|星期六, 11月8日 15:05:02|
|%w, %e %b %r %H:%M|星期六, 11月8日 15:05|
|%w, %e %b %r %H:%M %Z|11月8日 星期六 15:05 GMT|
|%e %b %b %r %H:%M:%s %Z|11月8日 15:05:02 GMT|
|%e %b %r %H:%M:%s"|11月8日 15:05:02|
|%e %b %r %H:%M"|11月8日 15:05|
|%e %b %r %H:%M %Z"|11月8日 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|[格式]|範例|
|------|-------|
|%w, %e-%b-%r %H:%M:%s %Z|星期六, 08-11月-14日 15:05:02 GMT|
|%w, %e-%b-%r %H:%M:%s|星期六, 08-11月-14日 15:05:02|
|%w, %e-%b-%r %H:%M %Z|星期六, 08-11月-14日 15:05 GMT|
|%w, %e-%b-%r %H:%M|星期六, 08-11月-14日 15:05|
|%e-%b-%r %H:%M:%s %Z|08-11月-14日 15:05:02 GMT|
|%e-%b-%r %H:%M:%s"|08-11月-14日 15:05:02|
|%e-%b-%r %H:%M %Z"|08-11月-14日 15:05 GMT|
|%e-%b-%r %H:%M"|08-11月-14日 15:05|


### <a name="sortable"></a>Sortable 

|[格式]|範例|
|------|-------|        
|%Y-%n-%e %H:%M:%s|2014-11-08 15:05:25|
|%Y-%n-%e %H:%M:%s %Z|2014-11-08 15:05:25 GMT|
|%Y-%n-%e %H:%M|2014-11-08 15:05|
|%Y-%n-%e %H:%M %Z|2014-11-08 15:05 GMT|
|%Y-%n-%eT%H:%M:%s|2014-11-08T15:05:25|
|%Y-%n-%eT%H:%M:%s %Z|2014-11-08T15:05:25 GMT|
|%Y-%n-%eT%H:%M|2014-11-08T15:05|
|%Y-%n-%eT%H:%M %Z"|2014-11-08T15:05 GMT|