---
title: funnel_sequence_completion 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 funnel_sequence_completion 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: 57cceb2fabb16956090430161b98c1287efdef97
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227313"
---
# <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion plugin

在比較不同的時間週期內，計算已完成順序步驟的漏斗圖。

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

**語法**

*T* `| evaluate` `funnel_sequence_completion(` *IdColumn* `,` *TimelineColumn* `,` *開始* `,` *結束* `,` *步驟* `,` *StateColumn* `,` *序列* `,` *MaxSequenceStepWindows*`)`

**引數**

* *T*：輸入表格式運算式。
* *IdColum*：資料行參考，必須存在於來源運算式中。
* *TimelineColumn*：代表時間軸的資料行參考必須存在於來源運算式中。
* *開始*：分析開始期間的純量常數值。
* *End*：分析結束期間的純量常數值。
* *步驟*：分析步驟期間（bin）的純量常數值。
* *StateColumn*：代表狀態的資料行參考，必須存在於來源運算式中。
* *Sequence*：具有順序值的常數動態陣列（在中查閱值 `StateColumn` ）。
* *MaxSequenceStepWindows*：純量常數動態陣列，其中包含序列中第一個和最後一個連續步驟之間的最大允許 timespan 值。 陣列中的每個視窗（期間）都會產生漏斗分析結果。

**傳回**

傳回單一資料表，適合用來針對分析的序列來建立漏斗圖：

* `TimelineColumn`：已分析的時間範圍
* `StateColumn`：順序的狀態。
* `Period`：用來完成從序列的第一個步驟測量之漏斗序列中步驟的最大期間（視窗）。 *MaxSequenceStepWindows*中的每個值都會產生一個不同期間的漏斗分析。 
* `dcount`： `IdColumn` 從第一個序列狀態轉換為值的時間範圍內的相異計數 `StateColumn` 。

**範例**

### <a name="exploring-storm-events"></a>探索風暴事件 

下列查詢會檢查序列的完成漏斗圖： `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` 在1hour、timegenerated>now-4hours、1day 的「整體」時間。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|`StartTime`|`EventType`|`Period`|`dcount`|
|---|---|---|---|
|2007-01-01 00：00：00.0000000|Hail|01:00:00|2877|
|2007-01-01 00：00：00.0000000|龍捲風|01:00:00|208|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|01:00:00|87|
|2007-01-01 00：00：00.0000000|Hail|04:00:00|2877|
|2007-01-01 00：00：00.0000000|龍捲風|04:00:00|231|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|04:00:00|141|
|2007-01-01 00：00：00.0000000|Hail|1.00：00：00|2877|
|2007-01-01 00：00：00.0000000|龍捲風|1.00：00：00|244|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|1.00：00：00|155|

瞭解結果：  
結果為三個漏斗圖（週期：一小時、4小時和一天）。 針對每個漏斗圖步驟，會顯示數個相異計數。 您可以看到為了完成整個序列所提供的時間愈久 `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` ，會取得較高的 `dcount` 值。 換句話說，序列到達漏斗步驟的次數會更多。
