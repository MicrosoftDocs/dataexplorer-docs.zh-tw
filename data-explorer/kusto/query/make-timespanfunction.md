---
title: make_timespan （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 make_timespan （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3904f852fdf813d8b2aff264d6b1bc0019335d78
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346944"
---
# <a name="make_timespan"></a>make_timespan()

從指定的時間週期建立[timespan](./scalar-data-types/timespan.md)純量值。

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

## <a name="syntax"></a>語法

`make_timespan(`*小時*、*分鐘*`)`

`make_timespan(`*hour*、*minute*、*second*`)`

`make_timespan(`*day*、*hour*、*minute*、*second*`)`

## <a name="arguments"></a>引數

* *day*： day （正整數值）
* *小時*：小時（從0到23的整數值）
* *分鐘*：分鐘（從0到59的整數值）
* *第*二個：第二個（實值，從0到59.9999999）

## <a name="returns"></a>傳回

如果建立成功，結果會是[timespan](./scalar-data-types/timespan.md)值，否則結果會是 null。
 
## <a name="example"></a>範例

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|時間範圍|
|---|
|1.12：30：55.1230000|


