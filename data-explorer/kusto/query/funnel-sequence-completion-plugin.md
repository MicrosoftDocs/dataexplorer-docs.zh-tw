---
title: funnel_sequence_completion 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 funnel_sequence_completion 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: cd4d0d07c191d70951c4cfafb549a2bc31f5d153
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249033"
---
# <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion plugin

在比較不同的時間週期內，計算已完成序列步驟的漏斗圖。

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

## <a name="syntax"></a>語法

*T* `| evaluate` `funnel_sequence_completion(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Step* `,` *state* `,` *Sequence* `,` *MaxSequenceStepWindows*`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColum*：資料行參考必須存在於來源運算式中。
* *TimelineColumn*：代表時間軸的資料行參考必須存在於來源運算式中。
* *開始*：分析開始期間的純量常數值。
* *End*：分析結束週期的純量常數值。
* *步驟*：分析步驟期間的純量常數值 (bin) 。
* *State*：代表狀態的資料行參考必須存在於來源運算式中。
* *Sequence*：) 中查閱順序值 (值的常數動態陣列 `StateColumn` 。
* *MaxSequenceStepWindows*：純量常數動態陣列，其值為序列中的第一個和最後一個順序步驟之間的最大允許 timespan。 陣列中的每個視窗 (期間) 都會產生漏斗分析結果。

## <a name="returns"></a>傳回

傳回單一資料表，適合用來針對分析的序列建立漏斗圖：

* `TimelineColumn`：分析的時間範圍
* `StateColumn`：順序的狀態。
* `Period`： [最長期間] (視窗) 可讓您完成從序列中的第一個步驟測量的漏斗序列步驟。 *MaxSequenceStepWindows*中的每個值都會產生具有個別句號的漏斗分析。 
* `dcount`： `IdColumn` 從第一個序列狀態轉換成值之時間範圍內的相異計數 `StateColumn` 。

## <a name="examples"></a>範例

### <a name="exploring-storm-events"></a>探索風暴活動 

下列查詢會檢查序列的完成漏斗圖： `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` 在1hour、timegenerated>now-4hours、1day 的「整體」時間中。 

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
結果是三個漏斗圖的週期 (：一小時、4小時和一天) 。 針對每個漏斗圖步驟，會顯示數個相異計數。 您可以看到，有更多時間可以完成的整個序列，則 `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` 會取得較高的 `dcount` 值。 換句話說，有更多的順序到達漏斗圖步驟。
