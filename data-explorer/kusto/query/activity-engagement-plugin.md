---
title: activity_engagement外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中activity_engagement外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7ed7ad7ebef425160b64b792ff75c95d4c4fcee
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030294"
---
# <a name="activity_engagement-plugin"></a>activity_engagement plugin

依據 ID 資料列來計算活動參與率隨滑動時間軸時段的變化。

activity_engagement外掛程式可用於計算 DAU/WAU/MAU(每日/每周/每月活動)。

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**語法**

*T* `| evaluate` T `,` *End* `,` `,` *dim1* `activity_engagement(` IdColumn*InnerActivityWindow*時間線列 。 開始結束 • 內部活動視窗外部活動視窗 • 暗 1 dim2 ...] `,` `,` *IdColumn* `,` `,` *Start* `,` *dim2* *TimelineColumn* *OuterActivityWindow*`)`

**引數**

* *T*: 輸入表格表示式。
* *IdColumn*: 具有表示使用者活動的 ID 值的欄的名稱。 
* *時間線列*:表示時間線的列的名稱。
* *開始*:(可選)具有分析開始週期的值的 Scalar。
* *結束*:(可選)具有分析結束週期的值的 Scalar。
* *內部活動視窗*:具有內範圍分析視窗期間值的 Scalar。
* *外部活動視窗*:具有外部範圍分析視窗期間值的 Scalar。
* *dim1* *,dim2*,...:(可選)列出對活動指標計算進行切片的維度列。

**傳回**

返回一個表,該表具有每個內部範圍視窗期間和每個現有維度組合的(內部範圍視窗中的 ID 值的不同計數、外部範圍視窗中的 ID 值不同計數以及活動比率)。

輸出表架構為:

|時間軸列|dcount_activities_inner|dcount_activities_outer|activity_ratio|昏暗1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|類型:截至*時間軸列*|long|long|double|..|..|..|


**範例**

### <a name="dauwau-calculation"></a>DAU/WAU 計算

以下範例計算 DAU/WAU(每日活躍使用者/每周活動使用者比率)對隨機生成的數據。

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="作用活動參與 dau wau":::

### <a name="daumau-calculation"></a>DAU/MAU 計算

以下範例計算 DAU/WAU(每日活躍使用者/每周活動使用者比率)對隨機生成的數據。

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d)
| project _day, Dau_Mau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="活動參與度 達烏毛":::

### <a name="daumau-calculation-with-additional-dimensions"></a>具有額外的 DAU/ MAU 計算

下面的範例計算 DAU/WAU(每日活躍使用者/每周活動使用者比率)與具有附加維度`mod3`() 的隨機生成數據。

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
| extend mod3 = strcat("mod3=", id % 3)
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d, mod3)
| project _day, Dau_Mau=activity_ratio*100, mod3 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="作用活動":::
