---
title: activity_engagement 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 activity_engagement 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cdee53ad7f46aacb71b8a8277e5b875e60438874
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349817"
---
# <a name="activity_engagement-plugin"></a>activity_engagement plugin

依據 ID 資料列來計算活動參與率隨滑動時間軸時段的變化。

activity_engagement 外掛程式可用於計算 DAU/WAU/MAU （每日/每週/每月活動）。

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `activity_engagement(` *IdColumn* `,` *TimelineColumn* `,` [*Start* `,` *End* `,` ] *InnerActivityWindow* `,` *OuterActivityWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColumn*：識別碼值代表使用者活動的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *Start*：（選擇性）流量分析開始期間的值來進行純量。
* *End*：（選擇性）以 [分析結束期間] 的值為純量。
* *InnerActivityWindow*：純量，具有內部範圍分析視窗期間的值。
* *OuterActivityWindow*：以 [外部範圍分析] 視窗期間的值為純量。
* *dim1*， *dim2*，...：（選擇性）分割活動度量計算的維度資料行清單。

## <a name="returns"></a>傳回

針對每個內部範圍視窗期間和每個現有的維度組合，傳回具有（內部範圍視窗內的識別碼值相異計數、外部範圍視窗內的識別碼值的相異計數，以及活動比率）的資料表。

輸出資料表架構為：

|TimelineColumn|dcount_activities_inner|dcount_activities_outer|activity_ratio|dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|類型：從*TimelineColumn*|long|long|double|..|..|..|


## <a name="examples"></a>範例

### <a name="dauwau-calculation"></a>DAU/WAU 計算

下列範例會計算隨機產生之資料的 DAU/WAU （每日作用中使用者/每週作用中使用者比率）。

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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="活動參與 dau wau":::

### <a name="daumau-calculation"></a>DAU/MAU 計算

下列範例會計算隨機產生之資料的 DAU/WAU （每日作用中使用者/每週作用中使用者比率）。

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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="活動參與 dau mau":::

### <a name="daumau-calculation-with-additional-dimensions"></a>具有其他維度的 DAU/MAU 計算

下列範例會使用額外的維度（），計算隨機產生之資料的 DAU/WAU （每日作用中使用者/每週作用中使用者比率） `mod3` 。

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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="活動參與 dau mau mod 3":::
