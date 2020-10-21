---
title: funnel_sequence 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 funnel_sequence 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 34159fb6d02cd30907924109c861d5e9fd963568
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252339"
---
# <a name="funnel_sequence-plugin"></a>funnel_sequence plugin

計算已採用一系列狀態之使用者的相異計數，以及先前/下一個/下一次/下一次或後續狀態的分佈。 

```kusto
T | evaluate funnel_sequence(id, datetime_column, startofday(ago(30d)), startofday(now()), 10m, 1d, state_column, dynamic(['S1', 'S2', 'S3']))
```

## <a name="syntax"></a>語法

*T* `| evaluate` `funnel_sequence(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *MaxSequenceStepWindow*、 *Step*、 *state*、 *Sequence*`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColum*：資料行參考必須存在於來源運算式中。
* *TimelineColumn*：代表時間軸的資料行參考必須存在於來源運算式中。
* *開始*：分析開始期間的純量常數值。
* *End*：分析結束週期的純量常數值。
* *MaxSequenceStepWindow*：序列中兩個連續步驟之間允許的最大 timespan 量值。
* *步驟*：分析步驟期間的純量常數值 (bin) 。
* *State*：代表狀態的資料行參考必須存在於來源運算式中。
* *Sequence*：) 中查閱順序值 (值的常數動態陣列 `StateColumn` 。

## <a name="returns"></a>傳回

傳回三個輸出資料表，這些資料表有助於用來針對分析的序列來建立 sankey 圖：

* 資料表 #1-上一個序列-下一個 `dcount` TimelineColumn：之前的分析時間範圍：先前的狀態 (可能是空的（如果有任何使用者只有搜尋的序列有事件，而不是在它) 之前的任何事件）。 
    下一步：如果有任何使用者只有搜尋的序列有事件，而不是任何其後) 的事件，則下一個狀態 (可能是空的。 
    `dcount`：已轉換之 `IdColumn` 時間範圍內的相異計數 `prev`  -->  `Sequence`  -->  `next` 。 
    範例：識別碼陣列 (從 `IdColumn` 對應到資料列順序的) ， (最大值為128的識別碼) 傳回。 

* 資料表 #2-前序 `dcount` TimelineColumn：之前的分析時間範圍：先前的狀態 (可能是空的。如果有任何使用者只有搜尋的序列有事件，而不是在它) 之前的任何事件，則為空白。 
    `dcount`：已轉換之 `IdColumn` 時間範圍內的相異計數 `prev`  -->  `Sequence`  -->  `next` 。 
    範例：識別碼陣列 (從 `IdColumn` 對應到資料列順序的) ， (最大值為128的識別碼) 傳回。 

* Table #3 sequence-next `dcount` TimelineColumn：接下來的分析時間間隔：下一個狀態 (如果有任何使用者只有搜尋順序的事件，而不是任何其後) 的事件，則會是空的。 
    `dcount`：已轉換之 `IdColumn` 時間範圍內的相異計數 `prev`  -->  `Sequence`  -->  `next` 。
    範例：識別碼陣列 (從 `IdColumn` 對應到資料列順序的) ， (最大值為128的識別碼) 傳回。 


## <a name="examples"></a>範例

### <a name="exploring-storm-events"></a>探索風暴活動 

下列查詢會查看資料表 StormEvents (2007) 的氣象統計資料，並顯示2007中發生所有龍捲風事件之前/之後發生的事件。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Looking on StormEvents statistics: 
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

結果包含三個數據表：

* 資料表 #1：在序列之前和之後發生的所有可能變異。 例如，第二行表示有87個具有下列順序的不同事件： `Hail` -> `Tornado` -> `Hail`


|`StartTime`|`prev`|`next`|`dcount`|
|---|---|---|---|
|2007-01-01 00：00：00.0000000|||293|
|2007-01-01 00：00：00.0000000|Hail|Hail|87|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|Thunderstorm Wind|77|
|2007-01-01 00：00：00.0000000|Hail|Thunderstorm Wind|28|
|2007-01-01 00：00：00.0000000|Hail||28|
|2007-01-01 00：00：00.0000000||Hail|27|
|2007-01-01 00：00：00.0000000||Thunderstorm Wind|25|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|Hail|24|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind||24|
|2007-01-01 00：00：00.0000000|Flash Flood|Flash Flood|12|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|Flash Flood|8|
|2007-01-01 00：00：00.0000000|Flash Flood||8|
|2007-01-01 00：00：00.0000000|漏斗雲端|Thunderstorm Wind|6|
|2007-01-01 00：00：00.0000000||漏斗雲端|6|
|2007-01-01 00：00：00.0000000||Flash Flood|6|
|2007-01-01 00：00：00.0000000|漏斗雲端|漏斗雲端|6|
|2007-01-01 00：00：00.0000000|Hail|Flash Flood|4|
|2007-01-01 00：00：00.0000000|Flash Flood|Thunderstorm Wind|4|
|2007-01-01 00：00：00.0000000|Hail|漏斗雲端|4|
|2007-01-01 00：00：00.0000000|漏斗雲端|Hail|4|
|2007-01-01 00：00：00.0000000|漏斗雲端||4|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|漏斗雲端|3|
|2007-01-01 00：00：00.0000000|大雨|Thunderstorm Wind|2|
|2007-01-01 00：00：00.0000000|Flash Flood|漏斗雲端|2|
|2007-01-01 00：00：00.0000000|Flash Flood|Hail|2|
|2007-01-01 00：00：00.0000000|強大風|Thunderstorm Wind|1|
|2007-01-01 00：00：00.0000000|大雨|Flash Flood|1|
|2007-01-01 00：00：00.0000000|大雨|Hail|1|
|2007-01-01 00：00：00.0000000|Hail|Flood|1|
|2007-01-01 00：00：00.0000000|閃電|Hail|1|
|2007-01-01 00：00：00.0000000|大雨|閃電|1|
|2007-01-01 00：00：00.0000000|漏斗雲端|大雨|1|
|2007-01-01 00：00：00.0000000|Flash Flood|Flood|1|
|2007-01-01 00：00：00.0000000|Flood|Flash Flood|1|
|2007-01-01 00：00：00.0000000||大雨|1|
|2007-01-01 00：00：00.0000000|漏斗雲端|閃電|1|
|2007-01-01 00：00：00.0000000|閃電|Thunderstorm Wind|1|
|2007-01-01 00：00：00.0000000|Flood|Thunderstorm Wind|1|
|2007-01-01 00：00：00.0000000|Hail|閃電|1|
|2007-01-01 00：00：00.0000000||閃電|1|
|2007-01-01 00：00：00.0000000|熱帶風暴|颶風 (颱風) |1|
|2007-01-01 00：00：00.0000000|近水樓臺洪水||1|
|2007-01-01 00：00：00.0000000|目前的 Rip||1|
|2007-01-01 00：00：00.0000000|大雪||1|
|2007-01-01 00：00：00.0000000|強大風||1|

* 資料表 #2：顯示依前一個事件分組的所有相異事件。 例如，第二行顯示已有總共發生過的150事件 `Hail` `Tornado` 。

|`StartTime`|`prev`|`dcount`|
|---------|-----|------|
|2007-01-01 00：00：00.0000000||331|
|2007-01-01 00：00：00.0000000|Hail|150|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|135|
|2007-01-01 00：00：00.0000000|Flash Flood|28|
|2007-01-01 00：00：00.0000000|漏斗雲端|22|
|2007-01-01 00：00：00.0000000|大雨|5|
|2007-01-01 00：00：00.0000000|Flood|2|
|2007-01-01 00：00：00.0000000|閃電|2|
|2007-01-01 00：00：00.0000000|強大風|2|
|2007-01-01 00：00：00.0000000|大雪|1|
|2007-01-01 00：00：00.0000000|目前的 Rip|1|
|2007-01-01 00：00：00.0000000|近水樓臺洪水|1|
|2007-01-01 00：00：00.0000000|熱帶風暴|1|

* 資料表 #3：顯示依下一個事件分組的所有相異事件。 例如，第二行顯示在之後發生的143事件總計 `Hail` `Tornado` 。

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00：00：00.0000000||332|
|2007-01-01 00：00：00.0000000|Hail|145|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|143|
|2007-01-01 00：00：00.0000000|Flash Flood|32|
|2007-01-01 00：00：00.0000000|漏斗雲端|21|
|2007-01-01 00：00：00.0000000|閃電|4|
|2007-01-01 00：00：00.0000000|大雨|2|
|2007-01-01 00：00：00.0000000|Flood|2|
|2007-01-01 00：00：00.0000000|颶風 (颱風) |1|

現在，讓我們試著找出下列順序如何繼續：  
`Hail` -> `Tornado` -> `Thunderstorm Wind`

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, 
dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']))
```

略過 `Table #1` 並 `Table #2` 看看 `Table #3` ，我們可以將 `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` 92 事件中的順序以這個順序結束，並在41事件中繼續進行， `Hail` 然後再回到 `Tornado` 14。

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00：00：00.0000000||92|
|2007-01-01 00：00：00.0000000|Hail|41|
|2007-01-01 00：00：00.0000000|龍捲風|14|
|2007-01-01 00：00：00.0000000|Flash Flood|11|
|2007-01-01 00：00：00.0000000|閃電|2|
|2007-01-01 00：00：00.0000000|大雨|1|
|2007-01-01 00：00：00.0000000|Flood|1|
