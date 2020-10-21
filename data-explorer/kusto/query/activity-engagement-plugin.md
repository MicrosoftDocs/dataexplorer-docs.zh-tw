---
title: activity_engagement 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 activity_engagement 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7c968470c772e977a8bdcfc5db3e4910b117ead
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247106"
---
# <a name="activity_engagement-plugin"></a>activity_engagement plugin

依據 ID 資料列來計算活動參與率隨滑動時間軸時段的變化。

activity_engagement 外掛程式可以用來計算每日/每週/每月活動) 的 DAU/WAU/MAU (。

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `activity_engagement(` *IdColumn* `,` *TimelineColumn* `,` [*Start* `,` *End* `,` ] *InnerActivityWindow* `,` *OuterActivityWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColumn*：具有代表使用者活動之識別碼值的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *啟動*： (選擇性) 純量值，並以分析開始期間的值。
* *End*： (選擇性的) 純量值，且其值為分析結束週期。
* *InnerActivityWindow*：以純量值為內部範圍分析視窗期間的純量值。
* *OuterActivityWindow*：以純量值取代外部範圍的分析視窗期間。
* *dim1*， *dim2*，...： () 選用的維度資料行清單來分割活動計量計算。

## <a name="returns"></a>傳回

傳回資料表，該資料表具有內部範圍視窗內的識別碼值 (相異計數、外部範圍視窗內的識別碼值相異計數，以及每個內部範圍視窗期間和每個現有維度組合的活動比率) 。

輸出資料表架構為：

|TimelineColumn|dcount_activities_inner|dcount_activities_outer|activity_ratio|dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|類型： *TimelineColumn*的|long|long|double|..|..|..|


## <a name="examples"></a>範例

### <a name="dauwau-calculation"></a>DAU/WAU 計算

下列範例會針對隨機產生的資料，計算 DAU/WAU (每日作用中使用者/每週作用中使用者比率) 。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="活動 engagement dau wau":::

### <a name="daumau-calculation"></a>DAU/MAU 計算

下列範例會針對隨機產生的資料，計算 DAU/WAU (每日作用中使用者/每週作用中使用者比率) 。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="活動 engagement dau wau":::

### <a name="daumau-calculation-with-additional-dimensions"></a>使用額外維度的 DAU/MAU 計算

下列範例會計算 DAU/WAU (每日作用中使用者/每週作用中使用者比率) 在具有額外維度 () 的隨機產生資料上 `mod3` 。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="活動 engagement dau wau":::
