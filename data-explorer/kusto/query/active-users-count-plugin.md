---
title: active_users_count 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 active_users_count 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a35cbb4c9078d58f2c9de3c681453c578baf1476
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252860"
---
# <a name="active_users_count-plugin"></a>active_users_count 外掛程式

計算值的相異計數，其中每個值都在回顧期間內至少出現了最少的週期數。

適用于僅計算「風扇」的相異計數，但不包含「非風扇」的外觀。 只有當使用者在回顧期間內處於作用中狀態時，才會將其視為「風扇」。 回顧期間只會用來判斷是否 ) 使用者被視為 `active` ( 「風扇」。 匯總本身不包含回顧視窗中的使用者。 相較之下， [sliding_window_counts](sliding-window-counts-plugin.md) 匯總是在回顧期間的滑動視窗上執行。

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `active_users_count(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *LookbackWindow* `,` *Period* `,` *ActivePeriodsCount* `,` *Bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColumn*：具有代表使用者活動之識別碼值的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *啟動*： (選擇性) 純量值，並以分析開始期間的值。
* *End*： (選擇性的) 純量值，且其值為分析結束週期。
* *LookbackWindow*：一個滑動時間範圍，定義檢查使用者外觀的期間。 回顧期限開始于 ( [目前的外觀]-[回顧視窗] ) 並以 ( [目前外觀] ) 結束。 
* *句號*：要計算為單一外觀的純量常數時間範圍 (如果使用者至少出現在此時間範圍內的不同 ActivePeriodsCount 中，則會被視為使用中。
* *ActivePeriodsCount*：要決定使用者是否在使用中的相異使用期間數目下限。 作用中使用者是指至少出現 (等於或大於) 使用中期間的使用者計數。
* *Bin*：分析步驟期間的純量常數值。 可以是數值/日期時間/時間戳記值，也可以是的字串 `week` / `month` / `year` 。 所有期間都將會是對應的[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md)函式。
* *dim1*， *dim2*，...： () 選用的維度資料行清單來分割活動計量計算。

## <a name="returns"></a>傳回

傳回資料表，其具有在下列期間內出現于 ActivePeriodCounts 中之識別碼的相異計數值：回顧期間、每個時間軸期間，以及每個現有的維度組合。

輸出資料表架構為：

|*TimelineColumn*|dim1|..|dim_n|dcount_values|
|---|---|---|---|---|
|類型： *TimelineColumn*的|..|..|..|long|


## <a name="examples"></a>範例

計算過去八天期間內至少出現三個不同日期的不同使用者數目。 分析期間：2018年7月。

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

|時間戳記|`dcount`|
|---|---|
|2018-07-01 00：00：00.0000000|1|
|2018-07-15 00：00：00.0000000|1|

如果使用者滿足下列兩個準則，則會被視為使用中： 
* 使用者至少會在三個不同的日子內看到 (Period = 1d，ActivePeriods = 3) 。
* 使用者會在8d 的回顧視窗中看到，並包含其目前的外觀。

在下圖中，此準則唯一有效的外觀是下列實例：在7/20 上的使用者 A 和7/4 上的使用者 B (查看上述的外掛程式結果) 。 使用者 B 的外觀包含在7/4 的回顧視窗中，但不適用於6/29-30 的 Start-End 時間範圍。 

:::image type="content" source="images/queries/active-users-count.png" alt-text="作用中使用者計數範例":::
