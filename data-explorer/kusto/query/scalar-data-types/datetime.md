---
title: Datetime 資料類型-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 datetime 資料類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: e65b16ac4a280f2ecbef2c4557cac4a8d7be13b3
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513126"
---
# <a name="the-datetime-data-type"></a>Datetime 資料類型

`datetime` (`date`) 資料類型代表時間的瞬間，通常表示為一天的日期和時間。
值的範圍是從 00:00:00 (午夜) ，0001年1月1日，西元西元 (的一般時代) 到西元11:59:59 年12月31日9999年12月31日 西曆 (西元 ) 。 

時間值是以 100-毫微秒單位（稱為刻度）來測量，而特定日期是自西元0001年1月1日午夜12:00 起的刻度數目。 GregorianCalendar 日曆中的 (西元 )  (不包括以閏秒數) 新增的刻度。
例如，刻度值31241376000000000代表日期、星期五、1月01日、0100 12:00:00 午夜。
這有時稱為「線性時間點」。

> [!WARNING]
> `datetime`Kusto 中的值一律會在 UTC 時區中。 顯示 `datetime` 其他時區的值是使用者應用程式的責任，會顯示資料，而不是資料本身的屬性。 如果需要將時區值保留為數據的一部分，則應該使用不同的資料行 (提供相對於 UTC) 的位移資訊。

## <a name="datetime-literals"></a>datetime 常值

型別的常 `datetime` 值具有語法 `datetime(` *值* `)` ，其中支援 *值* 的格式數，如下表所示：

|範例                                                     |值                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|時間一律是 UTC 格式。 省略日期則會提供今天的時間。|
|`datetime(null)`                                            |請參閱 [null 值](null-values.md)。                            |
|`now()`                                                     |目前的時間。                                             |
|`now(`-*timespan*`)`                                        |`now()-`*timespan*                                            |
|`ago(`*timespan*`)`                                         |`now()-`*timespan*                                            |

`now()` 並 `ago()` 指出 `datetime` 值與 Kusto 開始執行查詢時的時間點比較。 這些可能會在相同的查詢中出現多次，而單一值將用於全部。
 (換句話說，例如的運算式一律會 `now(-x) - ago(x)` 評估為零的 `timespan` 值。 ) 

## <a name="supported-formats"></a>支援的格式

有數種格式可 `datetime` 支援做為 [datetime ( # A1 常](#datetime-literals) 值和 [todatetime ( # B3 ](../todatetimefunction.md) 函數。

> [!WARNING]
> **強烈建議您** 只使用 ISO 8601 格式。

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|[格式]|範例|
|------|-------|
|% Y-% m-% dT% H:%M：% s% z|2014-05-25T08：20： 03.123456 Z|
|% Y-% m-% dT% H:%M：% s|2014-05-25T08：20：03.123456|
|% Y-% m-% dT% H:%M|2014-05-25T08：20|
|% Y-% m-% d% H:%M：% s% z|2014-11-08 15：55： 55.123456 z|
|% Y-% m-% d% H:%M：% s|2014-11-08 15:55:55|
|% Y-% m-% d% H:%M|2014-11-08 15:55|
|% Y-% m-% d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|[格式]|範例|
|------|-------|
|% w，% e% b% r% H:%M：% s% Z|週六，14 15:05:02 年11月8日 GMT|
|% w，% e% b% r% H:%M：% s|週六，14 15:05:02 年11月8日|
|% w，% e% b% r% H:%M|週六，14 15:05 年11月8日|
|% w，% e% b% r% H:%M% Z|週六，14 15:05 年11月8日 GMT|
|% e% b% r% H:%M：% s% Z|14 15:05:02 年11月8日 GMT|
|% e% b% r% H:%M：% s|14 15:05:02 年11月8日|
|% e% b% r% H:%M|14 15:05 年11月8日|
|% e% b% r% H:%M% Z|14 15:05 年11月8日 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|[格式]|範例|
|------|-------|
|% w，% e-% b-% r% H:%M：% s% Z|星期六，08年11月-14 15:05:02 GMT|
|% w，% e-% b-% r% H:%M：% s|星期六，08年11月-14 15:05:02|
|% w，% e-% b-% r% H:%M% Z|星期六，08年11月-14 15:05 GMT|
|% w，% e-% b-% r% H:%M|星期六，08年11月-14 15:05|
|% e-% b-% r% H:%M：% s% Z|08年11月-14 15:05:02 GMT|
|% e-% b-% r% H:%M：% s|08年11月-14 15:05:02|
|% e-% b-% r% H:%M% Z|08年11月-14 15:05 GMT|
|% e-% b-% r% H:%M|08年11月-14 15:05|


### <a name="sortable"></a>8601 

|[格式]|範例|
|------|-------|        
|% Y-% n-% e% H:%M：% s|2014-11-08 15:05:25|
|% Y-% n-% e% H:%M：% s% Z|2014-11-08 15:05:25 GMT|
|% Y-% n-% e% H:%M|2014-11-08 15:05|
|% Y-% n-% e% H:%M% Z|2014-11-08 15:05 GMT|
|% Y-% n-% eT% H:%M：% s|2014-11-08T15：05：25|
|% Y-% n-% eT% H:%M：% s% Z|2014-11-08T15：05： 25 GMT|
|% Y-% n-% eT% H:%M|2014-11-08T15：05|
|% Y-% n-% eT% H:%M% Z|2014-11-08T15： 05 GMT|
