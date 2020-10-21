---
title: session_count 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 session_count 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0790a3ab173bc653cbd3c4c15b3f28e5a0c70cd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252907"
---
# <a name="session_count-plugin"></a>session_count plugin

依據時間軸上的識別碼資料行計算會話計數。

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `session_count(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Bin* `,` *LookBackWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColumn*：具有代表使用者活動之識別碼值的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *啟動*：具有分析開始期間值的純量值。
* *End*：具有分析結束期間值的純量值。
* *Bin*：會話分析步驟期間的純量常數值。
* *LookBackWindow*：代表會話回顧期間的純量常數值。 如果中的識別碼 `IdColumn` 出現在的時間範圍內 `LookBackWindow` ，則會將該會話視為現有的。 如果未顯示識別碼，則會將會話視為新的。
* *dim1*， *dim2*，...： () 選用的維度資料行清單來分割會話計數計算。

## <a name="returns"></a>傳回

傳回資料表，其中包含每個時間軸期間的會話計數值，以及每個現有維度組合的會話計數值。

輸出資料表架構為：

|*TimelineColumn*|dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|類型： *TimelineColumn*的|..|..|..|long|


## <a name="examples"></a>範例

在此範例中，資料具有決定性，而且我們使用具有兩個數據行的資料表：
- 時間軸：從1到10000的執行中數位
- 識別碼：從1到50的使用者識別碼

`Id``Timeline`如果是 `Timeline` (時間軸% Id = = 0) 的分隔線，則會出現在特定位置。

具有的事件 `Id==1` 會出現在任何位置 `Timeline` 、 `Id==2` 每個第二個位置的事件， `Timeline` 依此類推。

以下是20行資料：

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|時間表|Id|
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

讓我們在下個詞彙中定義會話：只要使用者 (`Id`) 至少出現一次（在100個時間範圍內），而會話查閱視窗是41時間位置時，就會將會話視為作用中。

下一個查詢會根據上述定義來顯示作用中會話的計數。

<!-- csl: https://help.kusto.windows.net/Samples -->
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

:::image type="content" source="images/session-count-plugin/example-session-count.png" alt-text="會話計數範例" border="false":::
