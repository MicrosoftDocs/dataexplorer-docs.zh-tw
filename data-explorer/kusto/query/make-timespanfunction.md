---
title: 'make_timespan ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 make_timespan。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93b8ed12bcfb83c39964f820e61f928357af62c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253180"
---
# <a name="make_timespan"></a>make_timespan()

從指定的時段建立 [timespan](./scalar-data-types/timespan.md) 純量值。

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

## <a name="syntax"></a>語法

`make_timespan(`*小時*、*分鐘*`)`

`make_timespan(`*小時*、*分鐘*、*秒*`)`

`make_timespan(`*day*、*hour*、*minute*、*second*`)`

## <a name="arguments"></a>引數

* *day*： day (正整數值) 
* *小時*：小時 (整數值，從0到 23) 
* *分鐘*：分鐘 (整數值，從0到 59) 
* *第二個*：第二個 (實值，從0到 59.9999999) 

## <a name="returns"></a>傳回

如果建立成功，結果將會是 [timespan](./scalar-data-types/timespan.md) 值，否則結果會是 null。
 
## <a name="example"></a>範例

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|時間範圍|
|---|
|1.12：30：55.1230000|


