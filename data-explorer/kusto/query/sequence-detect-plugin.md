---
title: sequence_detect外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的sequence_detect外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afe4b41cc9bf3e81389edfb1fac76472772fa8f6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509175"
---
# <a name="sequence_detect-plugin"></a>sequence_detect外掛程式

根據提供的謂詞檢測序列匹配項。

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

**語法**

*T*`| evaluate`T`,``(`時間線`,`柱 最大序列步視窗最大序列跨度 Expr1 Expr2 ..., Dim1 Dim2 ... `sequence_detect` `,``,``,``,`*TimelineColumn*`,`*Dim2**Dim1**MaxSequenceSpan**Expr2**MaxSequenceStepWindow**Expr1*`)`

**引數**

* *T*: 輸入表格表示式。
* *時間軸列*:表示時間線的列引用,必須存在於源運算式中
* *最大序列步視窗*:序列中 2 個順序步驟之間允許的最大時間跨度的標量常數值
* *MaxSequenceSpan*: 最大跨度的標量常數值,用於序列完成所有步驟
* *Expr1*, *Expr2*, ...: 布爾謂詞運算式定義序列步驟
* *Dim1*, *Dim2*, ...: 用於關聯序列的維度運算式

**傳回**

傳回表中每行表示單序列出現的單一表:

* *Dim1* *,Dim2*,...:用於關聯序列的維度列。
* *Expr1*_*時間軸列* *,Expr2*_*時間線列*, ...: 具有時間值的列,表示每個序列步驟的時間線。
* *持續時間*:整體序列時間視窗

**範例**

### <a name="exploring-storm-events"></a>探索風暴事件 

下面的查詢將查看表 StormEvents(2007 年的天氣統計資訊),並顯示「過熱」序列在 5 天內跟「Wildfire」的情況。

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
|加州|2007-05-08 00:00:00.0000000|2007-05-08 16:02:00.0000000|16:02:00|
|加州|2007-05-08 00:00:00.0000000|2007-05-10 11:30:00.0000000|2.11:30:00|
|加州|2007-07-04 09:00:00.0000000|2007-07-05 23:01:00.0000000|1.14:01:00|
|南達科他州|2007-07-23 12:00:00.0000000|2007-07-27 09:00:00.0000000|3.21:00:00|
|德克薩斯州|2007-08-10 08:00:00.0000000|2007-08-11 13:56:00.0000000|1.05:56:00|
|加州|2007-08-31 08:00:00.0000000|2007-09-01 11:28:00.0000000|1.03:28:00|
|加州|2007-08-31 08:00:00.0000000|2007-09-02 13:30:00.0000000|2.05:30:00|
|加州|2007-09-02 12:00:00.0000000|2007-09-02 13:30:00.0000000|01:30:00|