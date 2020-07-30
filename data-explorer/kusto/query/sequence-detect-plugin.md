---
title: sequence_detect 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 sequence_detect 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7b4b3d2b43bea2eeb96c9bbca94131cb7887db8c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345686"
---
# <a name="sequence_detect-plugin"></a>sequence_detect 外掛程式

根據提供的述詞偵測順序出現次數。

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `sequence_detect` `(` *TimelineColumn* `,` *MaxSequenceStepWindow* `,` *MaxSequenceSpan* `,` *運算式*運算式 `,` *2* `,` ...， *Dim1* `,` *Dim2* `,` .。。`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *TimelineColumn*：代表時間軸的資料行參考必須存在於來源運算式中
* *MaxSequenceStepWindow*：序列中2個順序步驟之間允許的最大 timespan 時間戳記的純量常數值
* *MaxSequenceSpan*：序列的最大範圍的純量常數值，用以完成所有步驟
* *運算式類型 1*、*運算式 2*...：定義順序步驟的布林述詞運算式
* *Dim1*、 *Dim2*、...：用來相互關聯順序的維度運算式

## <a name="returns"></a>傳回

傳回單一資料表，其中資料表中的每個資料列都代表一個序列出現次數：

* *Dim1*、 *Dim2*、...：用來相互關聯順序的維度資料行。
* *運算式 1*-_*TimelineColumn*，*運算式*_*TimelineColumn*，...：具有時間值的資料行，代表每個順序步驟的時間軸。
* *持續時間*：整體序列時間範圍

## <a name="examples"></a>範例

### <a name="exploring-storm-events"></a>探索風暴事件 

下列查詢會查看資料表 StormEvents （2007的氣象統計資料），並顯示在5天內 ' 野火 ' 序列後面加上 ' 過熱 ' 的案例。

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

|State|heat_StartTime|wildfire_StartTime|Duration|
|---|---|---|---|
|加州|2007-05-08 00：00：00.0000000|2007-05-08 16：02：00.0000000|16:02:00|
|加州|2007-05-08 00：00：00.0000000|2007-05-10 11：30：00.0000000|2.11：30：00|
|加州|2007-07-04 09：00：00.0000000|2007-07-05 23：01：00.0000000|1.14：01：00|
|南北達科他|2007-07-23 12：00：00.0000000|2007-07-27 09：00：00.0000000|3.21：00：00|
|德克薩斯州|2007-08-10 08：00：00.0000000|2007-08-11 13：56：00.0000000|1.05：56：00|
|加州|2007-08-31 08：00：00.0000000|2007-09-01 11：28：00.0000000|1.03：28：00|
|加州|2007-08-31 08：00：00.0000000|2007-09-02 13：30：00.0000000|2.05：30：00|
|加州|2007-09-02 12：00：00.0000000|2007-09-02 13：30：00.0000000|01:30:00|
