---
title: make_timespan() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 make_timespan()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31301f684ea700cf89e9234c4c43adab068319b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512762"
---
# <a name="make_timespan"></a>make_timespan()

從指定的時間段創建[時間跨度](./scalar-data-types/timespan.md)標量值。

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

**語法**

`make_timespan(`*小時*,*分鐘*`)`

`make_timespan(`*小時*,*分鐘*,*秒*`)`

`make_timespan(`*天*,*小時*,*分鐘*,*秒*`)`

**引數**

* *日*: 天 (正整數值)
* *小時*:小時(整數值,從 0 到 23)
* *分鐘*:分鐘(整數值,從 0 到 59)
* *第*二個(實際值,從 0 到 59.999999)

**傳回**

如果創建成功,則結果將是一個[時間跨度](./scalar-data-types/timespan.md)值,否則結果將為空。
 
**範例**

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|時間範圍|
|---|
|1.12:30:55.1230000|


