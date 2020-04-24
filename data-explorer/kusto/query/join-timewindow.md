---
title: 在時間範圍內加入 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的時間範圍內聯接。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa3b81694714ef5af94407cdfdac263af0631e40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513357"
---
# <a name="joining-within-time-window"></a>在時間範圍內加入

通常,在一些高基數鍵(如操作 ID 或會話 ID)上的兩個大型數據集之間聯接通常很有用,並且通過添加對左側和`$right``$left``datetime`右側列之間的 「時間距離」限制,進一步限制需要匹配每個左側 () 記錄的右側 ( ) 記錄。 這與通常的 Kusto 聯接操作不同,因為除了「等聯接」部分(匹配高基數鍵或左右數據集)外,系統還可以應用距離函數並使用它大大加快聯接速度。 請注意,距離函數的均等性(即,當兩者都`dist(x,y)``dist(y,z)`為 true 時,則不遵循`dist(x,z)`也是如此 。*在內部,我們有時稱之為"對角連接"。*

例如,假設我們要在相對較小的時間視窗內標識事件序列。 為了演示此範例,假設我們有一個具有以下`T`架構的表:

- `SessionId`:`string`具有相關識別碼的類型欄。
- `EventType`:標識記錄的事件類型的`string`類型的列。
- `Timestamp`:`datetime`指示記錄描述的事件發生的時間的類型列。

```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
T
```

|SessionId|EventType|時間戳記|
|---|---|---|
|0|A|2017-10-01 00:00:00.0000000|
|0|B|2017-10-01 00:01:00.0000000|
|1|B|2017-10-01 00:02:00.0000000|
|1|A|2017-10-01 00:03:00.0000000|
|3|A|2017-10-01 00:04:00.0000000|
|3|B|2017-10-01 00:10:00.0000000|


**問題陳述**

我們希望我們的查詢回答以下問題:

   尋找事件類型`A`後跟時間視窗內`B``1min`事件類型的所有會話指示。

(在上面的範例資料中,唯一的此類會話`0`ID 是 .)

在語義上,以下查詢回答了這個問題,儘管效率不高:

```kusto
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp
    ) on SessionId
| where (End - Start) between (0min .. 1min)
| project SessionId, Start, End 

```

|SessionId|Start|結束|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|

為了優化此查詢,我們可以重寫它,如下所述,以便時間視窗表示為聯接鍵。

**重寫查詢以考慮時間視窗**

其思路是重寫查詢,以便`datetime`將值"離散"到大小為時間視窗一半的存儲桶中。
然後,我們可以使用 Kusto 的等聯接來比較這些存儲桶的 I。

```kusto
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0; // lookup bin = equal to 1/2 of the lookup window
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp,
          // TimeKey on the left side of the join is mapped to a discrete time axis for the join purpose
          TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              // TimeKey on the right side of the join - emulates event 'B' appearing several times
              // as if it was 'replicated'
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    // 'mv-expand' translates the TimeKey array range into a column
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|Start|結束|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**可執行查詢參考(表內)**

```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp, TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|Start|結束|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**50M 資料查詢**

下一個查詢類比 50M 記錄和 ±10M ID 的數據集,並使用上述技術運行查詢。

```kusto
let T = range x from 1 to 50000000 step 1
| extend SessionId = rand(10000000), EventType = rand(3), Time=datetime(2017-01-01)+(x * 10ms)
| extend EventType = case(EventType <= 1, "A",
                          EventType <= 2, "B",
                          "C");
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Time, TimeKey = bin(Time, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Time, 
              TimeKey = range(bin(Time-lookupWindow, lookupBin), 
                              bin(Time, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
| count 
```

|Count|
|---|
|1276|