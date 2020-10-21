---
title: sequence_detect 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 sequence_detect 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c284a1044e3e5a48cddeec352a945b48b665ce36
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250230"
---
# <a name="sequence_detect-plugin"></a>sequence_detect 外掛程式

根據提供的述詞偵測序列出現次數。

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `sequence_detect` `(` *TimelineColumn* `,` *MaxSequenceStepWindow* `,` *MaxSequenceSpan* `,` *運算式 1* - `,` *2* `,` ：..， *Dim1* `,` *Dim2* `,` .。。`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *TimelineColumn*：代表時間軸的資料行參考必須存在於來源運算式中
* *MaxSequenceStepWindow*：序列中2個連續步驟之間允許的最大 timespan 值的純量常數值
* *MaxSequenceSpan*：順序的最大值範圍的純量常數值，以完成所有步驟
* *運算式 1*：/ */運算式類型*：布林值述詞運算式定義順序步驟
* *Dim1*、 *Dim2*、...：用來相互關聯順序的維度運算式

## <a name="returns"></a>傳回

傳回單一資料表，其中資料表中的每個資料列都代表一個順序發生：

* *Dim1*、 *Dim2*、...：用來相互關聯順序的維度資料行。
* *運算式 1*：//_*TimelineColumn*，值*2-2*_*TimelineColumn*，...：具有時間值的資料行，代表每個序列步驟的時間軸。
* *持續時間*：整體序列時間範圍

## <a name="examples"></a>範例

### <a name="exploring-storm-events"></a>探索風暴活動 

下列查詢會在資料表 StormEvents (2007) 的氣象統計資料，並顯示在5天內「過高的熱度」順序在「Wildfire」之後的情況。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| evaluate sequence_detect(
        StartTime,
        5d,  // step max-time
        5d,  // sequence max-time
        heat=(EventType == "Excessive Heat"), 
        wildfire=(EventType == 'Wildfire'), 
        State)
```

|狀態|heat_StartTime|wildfire_StartTime|Duration|
|---|---|---|---|
|加州|2007-05-08 00：00：00.0000000|2007-05-08 16：02：00.0000000|16:02:00|
|加州|2007-05-08 00：00：00.0000000|2007-05-10 11：30：00.0000000|2.11：30：00|
|加州|2007-07-04 09：00：00.0000000|2007-07-05 23：01：00.0000000|1.14：01：00|
|南達科他州|2007-07-23 12：00：00.0000000|2007-07-27 09：00：00.0000000|3.21：00：00|
|德克薩斯州|2007-08-10 08：00：00.0000000|2007-08-11 13：56：00.0000000|1.05：56：00|
|加州|2007-08-31 08：00：00.0000000|2007-09-01 11：28：00.0000000|1.03：28：00|
|加州|2007-08-31 08：00：00.0000000|2007-09-02 13：30：00.0000000|2.05：30：00|
|加州|2007-09-02 12：00：00.0000000|2007-09-02 13：30：00.0000000|01:30:00|
