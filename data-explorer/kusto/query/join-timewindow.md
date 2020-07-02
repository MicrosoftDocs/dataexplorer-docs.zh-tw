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
ms.openlocfilehash: 1ca7f38fa377be40cd290b04af65cc6fd59075cd
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763709"
---
# <a name="time-window-join"></a>時段聯結

在某些高基數索引鍵上的兩個大型資料集之間聯結通常很有用，例如，作業識別碼或會話識別碼，並進一步限制需要與每個左側（$left）記錄相符的右手邊（$right）記錄，方法是在左側和右側的日期時間資料行之間新增「時間距離」的限制。

函數在聯結中很有用，如下列案例所示：
* 根據某些高基數索引鍵（例如作業識別碼或會話識別碼），在兩個大型資料集之間聯結。
* 限制需要與每個左側（$left）記錄相符的右手邊（$right）記錄，方法是在左側和右側的日期時間資料行之間新增「時間距離」的限制。

上述作業與一般的 Kusto 聯結作業不同，因為在 *`equi-join`* 左和右資料集之間比對高基數索引鍵的部分，系統也可以套用距離函數，並使用它來大幅加速聯結。

> [!NOTE]
> 距離函式的行為不像是相等（也就是說，當 dist （x，y）和 dist （y，z）都是 true 時，不會遵循該 dist （x，z）也是如此）。就內部而言，我們有時稱之為「對角聯結」。

例如，如果您想要在相對較小的時間範圍內識別事件序列，假設您有一個 `T` 具有下列架構的資料表：

* `SessionId`：具有相互關聯識別碼之類型的資料行 `string` 。
* `EventType`：類型的資料行 `string` ，可識別記錄的事件種類。
* `Timestamp`：類型的資料行 `datetime` 表示記錄所描述的事件發生的時間。

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

我們的查詢應該回答下列問題：

   尋找事件種類在 `A` `B` 某個時間範圍內的事件種類後面的所有會話識別碼 `1min` 。

> [!NOTE]
> 在上述的範例資料中，唯一的這類會話識別碼是 `0` 。

就語義而言，下列查詢會回答這個問題，雖然效率不低下。

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

|SessionId|開始|結束|
|---|---|---|
|0|2017-10-01 00：00：00.0000000|2017-10-01 00：01：00.0000000|

若要優化此查詢，我們可以如下所述重寫它，以便將時間範圍表示為聯結索引鍵。

**重寫查詢以考慮時間範圍**

重寫查詢，以便將 `datetime` 值「離散化」到大小為時間範圍一半的 bucket。 使用 Kusto 的 *`equi-join`* 來比較這些值區識別碼。

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

|SessionId|開始|結束|
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

|SessionId|開始|結束|
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

|Count|
|---|
|1276|
