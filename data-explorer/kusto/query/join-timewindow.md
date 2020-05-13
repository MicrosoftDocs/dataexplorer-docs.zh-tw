---
title: 在時間範圍內加入-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中的時間範圍內聯結。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4741da4367bb1a350c7310ea21ebe5ce9b91b06b
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271480"
---
# <a name="joining-within-time-window"></a>在時間範圍內加入

在某些高基數的索引鍵上聯結兩個大型資料集（例如作業識別碼或會話識別碼），並進一步限制 `$right` 需要針對每個左側（）記錄進行比對的右手邊（）記錄，方法是在 `$left` `datetime` 左邊和右邊的資料行之間加上「時間距離」的限制。 這不同于一般 Kusto 聯結作業，除了「等聯結」部分（符合高基數索引鍵或左和右資料集）之外，系統也可以套用距離函數，並使用它來大幅加速聯結。 請注意，距離函式的行為不像是相等的（也就是說，當 `dist(x,y)` 和 `dist(y,z)` 都是 true 時，也不會跟著 `dist(x,z)` 也是如此）。*就內部而言，我們有時稱之為「對角聯結」。*

例如，假設我們想要在相對較小的時間範圍內識別事件序列。 為了示範這個範例，假設我們有一個資料表， `T` 其中包含下列架構：

- `SessionId`：具有相互關聯識別碼之類型的資料行 `string` 。
- `EventType`：類型的資料行 `string` ，可識別記錄的事件種類。
- `Timestamp`：類型的資料行 `datetime` ，指出記錄所描述的事件發生的時間。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|0|A|2017-10-01 00：00：00.0000000|
|0|B|2017-10-01 00：01：00.0000000|
|1|B|2017-10-01 00：02：00.0000000|
|1|A|2017-10-01 00：03：00.0000000|
|3|A|2017-10-01 00：04：00.0000000|
|3|B|2017-10-01 00：10：00.0000000|


**問題陳述**

我們想要查詢回答下列問題：

   在時間範圍內，尋找事件種類 `A` 後面接著事件種類的所有會話 `B` 識別碼 `1min` 。

（在上述的範例資料中，唯一的這類會話識別碼是 `0` ）。

就語義而言，下列查詢會回答這個問題，雖然效率不低下：

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
|0|2017-10-01 00：00：00.0000000|2017-10-01 00：01：00.0000000|

若要優化此查詢，我們可以如下所述重寫它，以便將時間範圍表示為聯結索引鍵。

**重寫查詢以考慮時間範圍**

其概念是要重寫查詢，以便將 `datetime` 值「離散化」到大小為時間範圍一半的 bucket。
然後，我們可以使用 Kusto 的同等聯結來比較這些值區識別碼。

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
|0|2017-10-01 00：00：00.0000000|2017-10-01 00：01：00.0000000|

**可執行檔查詢參考（使用內嵌的資料表）**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|0|2017-10-01 00：00：00.0000000|2017-10-01 00：01：00.0000000|


**50M 資料查詢**

下一個查詢會模擬50M 記錄和 ~ 1 千萬個識別碼的資料集，並使用上述技術執行查詢。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

|計數|
|---|
|1276|
