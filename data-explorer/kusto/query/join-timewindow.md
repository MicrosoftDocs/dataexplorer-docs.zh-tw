---
title: 在時間範圍內加入-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中的時間範圍內加入。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b1f951f23587451d62deefa5feb24e2d1fc6b612
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251106"
---
# <a name="time-window-join"></a>時段聯結

在某些高基數索引鍵上聯結兩個大型資料集通常很有用。例如，作業識別碼或會話識別碼，並進一步限制右邊的 ($right) 記錄，這些記錄必須與每一側左邊的 ($left) 記錄一致，方法是在左邊和右邊的日期時間資料行之間新增「時間距離」限制。

函數在聯結方面很有用，如下列案例所示：
* 根據某些高基數索引鍵（例如作業識別碼或會話識別碼）來聯結兩個大型資料集。
* 限制右邊的 ($right) 記錄必須與每個左側 ($left) 記錄相符，方法是在左邊和右邊的日期時間資料行之間新增「時間距離」限制。

上述作業與一般 Kusto 聯結作業不同，因為在比對 *`equi-join`* 左邊和右邊資料集之間的高基數索引鍵時，系統也可以套用距離函數，並使用它來大幅加速聯結。

> [!NOTE]
> 距離函式的行為不像相等 (亦即，當 dist (x、y) 和 dist (y，z) 為 true 時，不會遵循該 dist (x，z) 也是 true。在內部，我們有時會將此視為「對角線聯結」。

例如，如果您想要在相對較短的時間範圍內識別事件順序，假設您有一個 `T` 具有下列架構的資料表：

* `SessionId`：具有相互關聯識別碼之類型的資料行 `string` 。
* `EventType`： `string` 識別記錄之事件種類的類型資料行。
* `Timestamp`：類型的資料行， `datetime` 指出記錄所描述的事件發生的時間。

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

我們的查詢應該會回答下列問題：

   尋找事件種類在 `A` `B` 時間範圍內的事件種類之後所使用的所有會話識別碼 `1min` 。

> [!NOTE]
> 在上述的範例資料中，唯一的這類會話識別碼是 `0` 。

在語義上，下列查詢會回答這個問題，雖然效率不會。

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

若要優化此查詢，我們可以如下所述加以重寫，讓時間範圍以聯結索引鍵表示。

**將查詢重寫為時間範圍的帳戶**

重寫查詢，以便將 `datetime` 值「離散化」到大小是時間範圍一半的值區。 使用 Kusto *`equi-join`* 來比較這些 Bucket 識別碼。

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

**內嵌資料表) 可執行檔查詢參考 (**

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

下一個查詢會模擬50M 記錄的資料集和 ~ 1 千萬個識別碼，並使用上述技術來執行查詢。

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
