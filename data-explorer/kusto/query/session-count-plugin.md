---
title: session_count外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的session_count外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76ce6ca3be1859aba0a94ae155c60342be3f1ad1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663140"
---
# <a name="session_count-plugin"></a>session_count plugin

根據時間線上的 ID 列計算會話計數。

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` T `,` *End* `,` `,` *IdColumn* `session_count(` IdColumn*dim2*時間線列結束箱搜尋窗 = dim1 dim2 ...] `,` `,` *Bin* `,` *Start* `,` *TimelineColumn* `,` *LookBackWindow* *dim1*`)`

**引數**

* *T*: 輸入表格表示式。
* *IdColumn*: 具有表示使用者活動的 ID 值的欄的名稱。 
* *時間線列*:表示時間線的列的名稱。
* *開始*:具有分析開始期值的 Scalar。
* *結束*:具有分析結束週期值的 Scalar。
* *:會話*分析步驟周期的標量常量值。
* *LookBackWindow*:表示會話回顧周期的標量常量值。 如果`IdColumn`id`LookBackWindow`出現在時間 視窗中 - 會話被視為現有,如果沒有 - 會話被視為新會話。
* *dim1,dim2*,...:(可選)包含會話計數計算的維度列清單*dim2*。

**傳回**

返回具有每個時間線期間和每個現有維度組合的會話計數值的表。

輸出表架構為:

|*時間軸列*|昏暗1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|類型:截至*時間軸列*|..|..|..|long|


**範例**


為了這個範例,我們將建立資料確定性 - 包含兩列的表:
- 時間軸:從 1 到 10,000 的執行編號
- ID: 使用者代碼從 1 到 50

`Id`如果是分隔`Timeline`符`Timeline`(時間軸% ID = 0),則出現在特定插槽中。

`Id==1`這意味著,事件將出現在`Timeline`任何插槽中,事件`Id==2`與每秒`Timeline`插槽,等等。

以下是以下 20 行資料:

```kusto
let _data = range Timeline from 1 to 10000 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// Look on few lines of the data
_data
| order by Timeline asc, Id asc
| limit 20
```

|時間軸|Id|
|---|---|
|1|1|
|2|1|
|2|2|
|3|1|
|3|3|
|4|1|
|4|2|
|4|4|
|5|1|
|5|5|
|6|1|
|6|2|
|6|3|
|6|6|
|7|1|
|7|7|
|8|1|
|8|2|
|8|4|
|8|8|

讓我們用下一個術語定義一個會話:只要使用者`Id`( ) 在 100 個時間槽的時間範圍內至少出現一次,而會話回視視窗為 41 個時隙,則被視為處於活動狀態的會話。

下一個查詢根據上述定義顯示活動會話的計數。

```kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart 
```

:::image type="content" source="images/queries/example-session-count.png" alt-text="工作階段計數範例":::
