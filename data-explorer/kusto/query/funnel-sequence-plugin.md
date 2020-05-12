---
title: funnel_sequence 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 funnel_sequence 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c68cac70223b4779b4ca0acf33cd9f66d8c91765
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227395"
---
# <a name="funnel_sequence-plugin"></a>funnel_sequence plugin

計算已採用一系列狀態的使用者相異計數，以及後續/下一個/後續狀態的分佈，其後面接著順序。 

```kusto
T | evaluate funnel_sequence(id, datetime_column, startofday(ago(30d)), startofday(now()), 10m, 1d, state_column, dynamic(['S1', 'S2', 'S3']))
```

**語法**

*T* `| evaluate` `funnel_sequence(` *IdColumn* `,` *TimelineColumn* `,` *開始* `,` *結束* `,` *MaxSequenceStepWindow*、*步驟*、 *StateColumn*、*順序*`)`

**引數**

* *T*：輸入表格式運算式。
* *IdColum*：資料行參考，必須存在於來源運算式中。
* *TimelineColumn*：代表時間軸的資料行參考必須存在於來源運算式中。
* *開始*：分析開始期間的純量常數值。
* *End*：分析結束期間的純量常數值。
* *MaxSequenceStepWindow*：序列中兩個順序步驟之間最大允許時間範圍的純量常數值。
* *步驟*：分析步驟期間（bin）的純量常數值。
* *StateColumn*：代表狀態的資料行參考，必須存在於來源運算式中。
* *Sequence*：具有順序值的常數動態陣列（在中查閱值 `StateColumn` ）。

**傳回**

會傳回三個輸出資料表，這適用于針對分析的序列來建立 sankey 圖：

* 資料表 #1-上一個序列-下一個 `dcount` TimelineColumn：之前的分析時間範圍：上一個狀態（如果有任何使用者只擁有搜尋序列的事件，而不是任何其之前的事件），則為空白。 
    下一步：下一個狀態（如果有任何使用者只有搜尋的序列有事件，而不是任何後面的事件），則會是空的。 
    `dcount`： `IdColumn` 轉換的時間範圍內的相異計數 `prev`  -->  `Sequence`  -->  `next` 。 
    範例：對應到資料列序列的識別碼（來自 `IdColumn` ）陣列（最多會傳回128個識別碼）。 

* 資料表 #2-上一個序列 `dcount` TimelineColumn：之前的分析時間範圍：上一個狀態（如果有任何使用者只有搜尋的序列有事件，而不是任何其之前的事件），則為空白。 
    `dcount`： `IdColumn` 轉換的時間範圍內的相異計數 `prev`  -->  `Sequence`  -->  `next` 。 
    範例：對應到資料列序列的識別碼（來自 `IdColumn` ）陣列（最多會傳回128個識別碼）。 

* 資料表 #3 順序-下一個 `dcount` TimelineColumn：已分析的時間範圍下一個：下一個狀態（如果有任何使用者只擁有搜尋序列的事件，而不是任何後面的事件），則會是空的。 
    `dcount`： `IdColumn` 轉換的時間範圍內的相異計數 `prev`  -->  `Sequence`  -->  `next` 。
    範例：對應到資料列序列的識別碼（來自 `IdColumn` ）陣列（最多會傳回128個識別碼）。 


**範例**

### <a name="exploring-storm-events"></a>探索風暴事件 

下列查詢會查看資料表 StormEvents （2007的氣象統計資料），並顯示在2007中發生所有龍捲風事件之前/之後發生的事件。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Looking on StormEvents statistics: 
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

結果包含三個數據表：

* 資料表 #1：序列前後發生的所有可能變體。 例如，第二行表示有87具有下列順序的不同事件：`Hail` -> `Tornado` -> `Hail`


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
|2007-01-01 00：00：00.0000000|繁重 Rain|Thunderstorm Wind|2|
|2007-01-01 00：00：00.0000000|Flash Flood|漏斗雲端|2|
|2007-01-01 00：00：00.0000000|Flash Flood|Hail|2|
|2007-01-01 00：00：00.0000000|強式風|Thunderstorm Wind|1|
|2007-01-01 00：00：00.0000000|繁重 Rain|Flash Flood|1|
|2007-01-01 00：00：00.0000000|繁重 Rain|Hail|1|
|2007-01-01 00：00：00.0000000|Hail|Flood|1|
|2007-01-01 00：00：00.0000000|卻|Hail|1|
|2007-01-01 00：00：00.0000000|繁重 Rain|卻|1|
|2007-01-01 00：00：00.0000000|漏斗雲端|繁重 Rain|1|
|2007-01-01 00：00：00.0000000|Flash Flood|Flood|1|
|2007-01-01 00：00：00.0000000|Flood|Flash Flood|1|
|2007-01-01 00：00：00.0000000||繁重 Rain|1|
|2007-01-01 00：00：00.0000000|漏斗雲端|卻|1|
|2007-01-01 00：00：00.0000000|卻|Thunderstorm Wind|1|
|2007-01-01 00：00：00.0000000|Flood|Thunderstorm Wind|1|
|2007-01-01 00：00：00.0000000|Hail|卻|1|
|2007-01-01 00：00：00.0000000||卻|1|
|2007-01-01 00：00：00.0000000|Tropical 風暴|颶風（颱風）|1|
|2007-01-01 00：00：00.0000000|近水樓臺水災||1|
|2007-01-01 00：00：00.0000000|目前的 Rip||1|
|2007-01-01 00：00：00.0000000|繁重的雪||1|
|2007-01-01 00：00：00.0000000|強式風||1|

* 資料表 #2：顯示依前一個事件分組的所有相異事件。 例如，第二行會顯示過去發生的150事件總數 `Hail` `Tornado` 。

|`StartTime`|`prev`|`dcount`|
|---------|-----|------|
|2007-01-01 00：00：00.0000000||331|
|2007-01-01 00：00：00.0000000|Hail|150|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|135|
|2007-01-01 00：00：00.0000000|Flash Flood|28|
|2007-01-01 00：00：00.0000000|漏斗雲端|22|
|2007-01-01 00：00：00.0000000|繁重 Rain|5|
|2007-01-01 00：00：00.0000000|Flood|2|
|2007-01-01 00：00：00.0000000|卻|2|
|2007-01-01 00：00：00.0000000|強式風|2|
|2007-01-01 00：00：00.0000000|繁重的雪|1|
|2007-01-01 00：00：00.0000000|目前的 Rip|1|
|2007-01-01 00：00：00.0000000|近水樓臺水災|1|
|2007-01-01 00：00：00.0000000|Tropical 風暴|1|

* 資料表 #3：顯示依下一個事件分組的所有相異事件。 例如，第二行顯示 `Hail` 在之後發生的143事件總數 `Tornado` 。

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00：00：00.0000000||332|
|2007-01-01 00：00：00.0000000|Hail|145|
|2007-01-01 00：00：00.0000000|Thunderstorm Wind|143|
|2007-01-01 00：00：00.0000000|Flash Flood|32|
|2007-01-01 00：00：00.0000000|漏斗雲端|21|
|2007-01-01 00：00：00.0000000|卻|4|
|2007-01-01 00：00：00.0000000|繁重 Rain|2|
|2007-01-01 00：00：00.0000000|Flood|2|
|2007-01-01 00：00：00.0000000|颶風（颱風）|1|

現在，讓我們試著找出下列順序如何繼續：  
`Hail` -> `Tornado` -> `Thunderstorm Wind`

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, 
dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']))
```

略過 `Table #1` 並 `Table #2` 查看， `Table #3` 我們可以結束 `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` 在92事件中以這個順序結束的順序，並以41事件的形式繼續， `Hail` 然後回到 `Tornado` 14 中。

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00：00：00.0000000||92|
|2007-01-01 00：00：00.0000000|Hail|41|
|2007-01-01 00：00：00.0000000|龍捲風|14|
|2007-01-01 00：00：00.0000000|Flash Flood|11|
|2007-01-01 00：00：00.0000000|卻|2|
|2007-01-01 00：00：00.0000000|繁重 Rain|1|
|2007-01-01 00：00：00.0000000|Flood|1|
