---
title: funnel_sequence_completion外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的funnel_sequence_completion外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: 7f168841db2df47e4e3a192b75585a1fe4d83718
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514768"
---
# <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion plugin

計算比較不同時間段內已完成序列步驟的漏鬥。

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

**語法**

*T* `| evaluate` T *End* `,` *Sequence* *IdColumn* `,` `funnel_sequence_completion(` IdColumn*MaxSequenceStepWindows*時間線列 開始步驟 狀態列序列 最大序列步驟視窗`,`*Step*`,`*Start*`,`*TimelineColumn*`,`*StateColumn*`,``)`

**引數**

* *T*: 輸入表格表示式。
* *IdColum*:列引用,必須存在於源運算式中
* *時間軸列*:表示時間線的列引用,必須存在於源運算式中
* *開始*:分析開始期間的標量常數值
* *結束*:分析結束週期的標量常數值
* *步驟*:分析步長週期的標量常數值(bin) 
* *狀態列*:表示狀態的列引用,必須存在於源運算式中
* *序列*:具有序列值的常量動態陣列(值在`StateColumn`) 中抬起來。
* *MaxSequenceStepWindows*: 標量常量動態陣列,在序列中的第一個和最後一個順序步驟之間具有允許的最大時間跨度的值,陣列中的每個視窗(週期)生成漏鬥分析結果

**傳回**

傳回可用於為分析序列建構漏鬥圖的單表:

* 時間軸列:分析的時間視窗
* `StateColumn`:序列的狀態。
* 週期:允許完成從序列中第一步測量的漏鬥序列中的最大週期(視窗)。 *MaxSequenceStepWindows*中的每個值都會生成具有單獨週期的漏鬥分析。 
* 計數:從第一個`IdColumn`序列狀態轉換為 值 的時間`StateColumn`視窗的不同 計數。

**範例**

### <a name="exploring-storm-events"></a>探索風暴事件 

以下查詢檢查序列的完成漏鬥:`Hail` -> `Tornado` -> `Thunderstorm Wind`在「總體」時間為 1 小時、4 小時、1 天。 

```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|StartTime|EventType|期間|dcount|
|---|---|---|---|
|2007-01-01 00:00:00.0000000|Hail|01:00:00|2877|
|2007-01-01 00:00:00.0000000|龍捲風|01:00:00|208|
|2007-01-01 00:00:00.0000000|Thunderstorm Wind|01:00:00|87|
|2007-01-01 00:00:00.0000000|Hail|04:00:00|2877|
|2007-01-01 00:00:00.0000000|龍捲風|04:00:00|231|
|2007-01-01 00:00:00.0000000|Thunderstorm Wind|04:00:00|141|
|2007-01-01 00:00:00.0000000|Hail|1.00:00:00|2877|
|2007-01-01 00:00:00.0000000|龍捲風|1.00:00:00|244|
|2007-01-01 00:00:00.0000000|Thunderstorm Wind|1.00:00:00|155|

瞭解結果:  
結果它3個漏鬥(週期:1小時,4小時和1天),而每個漏鬥步驟顯示一些不同的事件單一計數。 `Thunderstorm Wind``dcount`您可以看到,完成較高 值的整個序列的時間就越長(這意味著到達漏鬥步驟的序列發生次數越多)。 `Hail`  ->  `Tornado`  -> 
