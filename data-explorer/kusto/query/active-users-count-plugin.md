---
title: active_users_count外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的active_users_count外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f324507d1a4528c5efefc14f7820437383211ca6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519341"
---
# <a name="active_users_count-plugin"></a>active_users_count外掛程式

計算不同的值計數,其中每個值在回顧期間至少以最小周期數出現。

可用於僅計算「粉絲」的不同計數,同時不包括「非粉絲」的外觀。 僅當使用者在回望期間處於活動狀態時,才會將用戶計為"風扇"。 回望期僅用於確定是否考慮`active`使用者("風扇")。 聚合本身不包括來自回望視窗的使用者(與聚合位於回望期的滑動視窗的 [sliding_window_counts] (sliding-window-counts-plugin.md) 不同)。

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` T *End* `,` *Bin* *Start* `,` `,` `active_users_count(` IdColumn*dim1*時間線列開始回顧視窗期間活動週期計數 Bin = dim1 dim2 ...] *Period* *IdColumn* `,` `,` `,` `,` `,` *dim2* `,` *TimelineColumn* `,` *LookbackWindow* *ActivePeriodsCount*`)`

**引數**

* *T*: 輸入表格表示式。
* *IdColumn*: 具有表示使用者活動的 ID 值的欄的名稱。 
* *時間線列*:表示時間線的列的名稱。
* *開始*:(可選)具有分析開始週期的值的 Scalar。
* *結束*:(可選)具有分析結束週期的值的 Scalar。
* *回望視窗*:定義檢查用戶外觀的時間段的滑動時間視窗。 回望期從 ([當前外觀] - [回視視窗])開始,到([當前外觀])結束。 
* *期間*: Scalar 常量時間跨度以計數為單個外觀(如果用戶出現在此時間跨度中至少不同的活動週期計數中,則使用者將計為活動。
* *活動期間計數*:用於決定使用者是否處於活動狀態的不同活動期間數最小。 活動使用者是指出現在至少(等於或大於)活動期間計數中的使用者。
* *:分析*步長週期的 Scalar 常量值。 可以是數字/日期時間/時間`week`/`month`/`year`戳值,也可以是 中的字串,在這種情況下,所有期間都將相應地作為[開始的](startofmonthfunction.md)/[周](startofweekfunction.md)/開始[。](startofyearfunction.md)
* *dim1* *,dim2*,...:(可選)列出對活動指標計算進行切片的維度列。

**傳回**

返回具有在回顧期間、每個時間線期間和每個現有維度組合在 ActivePeriodCounts 中出現的 Id 的不同計數值的表。

輸出表架構為:

|*時間軸列*|昏暗1|..|dim_n|dcount_values|
|---|---|---|---|---|
|類型:截至*時間軸列*|..|..|..|long|


**範例**

計算至少在過去 8 天內出現在 3 個不同日期的不同使用者的每周數量。 分析期:2018年7月。

```kusto
let Start = datetime(2018-07-01);
let End = datetime(2018-07-31);
let LookbackWindow = 8d;
let Period = 1d;
let ActivePeriods = 3;
let Bin = 7d; 
let T =  datatable(User:string, Timestamp:datetime)
[
    "B",      datetime(2018-06-29),
    "B",      datetime(2018-06-30),
    "A",      datetime(2018-07-02),
    "B",      datetime(2018-07-04),
    "B",      datetime(2018-07-08),
    "A",      datetime(2018-07-10),
    "A",      datetime(2018-07-14),
    "A",      datetime(2018-07-17),
    "A",      datetime(2018-07-20),
    "B",      datetime(2018-07-24)
]; 
T | evaluate active_users_count(User, Timestamp, Start, End, LookbackWindow, Period, ActivePeriods, Bin)



```

|時間戳記|dcount|
|---|---|
|2018-07-01 00:00:00.0000000|1|
|2018-07-15 00:00:00.0000000|1|

如果使用者在至少 3 個不同的天(期間 = 1d,ActivePeriods=3)中看到它,則被視為活動,該視窗位於當前外觀之前的 8d(包括當前外觀)。 在下圖中,根據此條件處於活動狀態的唯一外觀是 7/20 上的使用者 A 和 7/4 上的使用者 B(請參閱上面的外掛程式結果)。 請注意,儘管使用者 B 在 6/29-30 上的外觀不在開始-結束時間範圍內,但它們包含在 7/4 上的使用者 B 的回回視窗中。 

![alt 文字](images/queries/active-users-count.png "活動使用者計數")