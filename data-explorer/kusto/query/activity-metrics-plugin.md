---
title: activity_metrics外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的activity_metrics外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 940320b7cd77ba09b31192853da9073511b33f5d
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030243"
---
# <a name="activity_metrics-plugin"></a>activity_metrics plugin

根據當前期間視窗與上一期間視窗(與每次視窗*與所有上一*時間視窗進行比較[的activity_counts_metrics外掛程式](activity-counts-metrics-plugin.md))計算有用的活動指標(不同的計數值、新值的不同計數、保留率和改動率)。

```kusto
T | evaluate activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` T `,` `,` *End* `,` `,` `activity_metrics(` IdColumn 時間線列 • 開始結束 • 視窗 • 暗 1 dim2 ...] `,` `,` *IdColumn* `,` *Start* *Window* *TimelineColumn* *dim2* *dim1*`)`

**引數**

* *T*: 輸入表格表示式。
* *IdColumn*: 具有表示使用者活動的 ID 值的欄的名稱。 
* *時間線列*:表示時間線的列的名稱。
* *開始*:(可選)具有分析開始週期的值的 Scalar。
* *結束*:(可選)具有分析結束週期的值的 Scalar。
* *視窗*:具有分析視窗期間值的 Scalar。 可以是數字/日期時間/時間`week`/`month`/`year`戳值,也可以是 中的字串,在這種情況下,所有期間都將相應地作為[開始的](startofmonthfunction.md)/[周](startofweekfunction.md)/開始[。](startofyearfunction.md) 
* *dim1* *,dim2*,...:(可選)列出對活動指標計算進行切片的維度列。

**傳回**

返回具有不同計數值、每個時間線期間和每個現有維度組合的新值、保留率和改動率的不同計數的表。

輸出表架構為:

|*時間軸列*|dcount_values|dcount_newvalues|retention_rate|churn_rate|昏暗1|..|dim_n|
|---|---|---|---|---|--|--|--|--|--|--|
|類型:截至*時間軸列*|long|long|double|double|..|..|..|

**注意事項**

***保留率定義***

`Retention Rate`在一段時間內計算為:

    # of customers returned during the period
    / (divided by)
    # customers at the beginning of the period

其中`# of customers returned during the period`定義為:

    # of customers at end of period
    - (minus)
    # of new customers acquired during the period

`Retention Rate`範圍從 0.0 到 1.0  
分數越高,表示返回的使用者量越大。


***流失率定義***

`Churn Rate`在一段時間內計算為:
    
    # of customers lost in the period
    / (divided by)
    # of customers at the beginning of the period

其中`# of customer lost in the period`定義為:

    # of customers at the beginning of the period
    - (minus)
    # of customers at the end of the period

`Churn Rate`範圍從 0.0 到 1.0 越高的分數意味著更多的使用者不會返回服務。

***流失與保留率***

衍生的自`Churn Rate`與`Retention Rate`的定義 , 始終為 true:

    [Retention rate] = 100.0% - [Churn Rate]


**範例**

### <a name="weekly-retention-rate-and-churn-rate"></a>每週保留率和流失率

下一個查詢計算每周視窗的保留率和流失率。

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate, churn_rate
| render timechart 
```

|_day|retention_rate|churn_rate|
|---|---|---|
|2017-01-02 00:00:00.0000000|NaN|NaN|
|2017-01-09 00:00:00.0000000|0.179910044977511|0.820089955022489|
|2017-01-16 00:00:00.0000000|0.744374437443744|0.255625562556256|
|2017-01-23 00:00:00.0000000|0.612096774193548|0.387903225806452|
|2017-01-30 00:00:00.0000000|0.681141439205955|0.318858560794045|
|2017-02-06 00:00:00.0000000|0.278145695364238|0.721854304635762|
|2017-02-13 00:00:00.0000000|0.223172628304821|0.776827371695179|
|2017-02-20 00:00:00.0000000|0.38|0.62|
|2017-02-27 00:00:00.0000000|0.295519001701645|0.704480998298355|
|2017-03-06 00:00:00.0000000|0.280387770320656|0.719612229679344|
|2017-03-13 00:00:00.0000000|0.360628154795289|0.639371845204711|
|2017-03-20 00:00:00.0000000|0.288008028098344|0.711991971901656|
|2017-03-27 00:00:00.0000000|0.306134969325153|0.693865030674847|
|2017-04-03 00:00:00.0000000|0.356866537717602|0.643133462282398|
|2017-04-10 00:00:00.0000000|0.495098039215686|0.504901960784314|
|2017-04-17 00:00:00.0000000|0.198296836982968|0.801703163017032|
|2017-04-24 00:00:00.0000000|0.0618811881188119|0.938118811881188|
|2017-05-01 00:00:00.0000000|0.204657727593507|0.795342272406493|
|2017-05-08 00:00:00.0000000|0.517391304347826|0.482608695652174|
|2017-05-15 00:00:00.0000000|0.143667296786389|0.856332703213611|
|2017-05-22 00:00:00.0000000|0.199122325836533|0.800877674163467|
|2017-05-29 00:00:00.0000000|0.063468992248062|0.936531007751938|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-churn-and-retention.png" border="false" alt-text="活動指標產生及保留":::

### <a name="distinct-values-and-distinct-new-values"></a>不同的值和不同的「新」值 

下一個查詢計算每周視窗的不同值和"新"值(未出現在以前的時間視窗中的 ID)。


```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, dcount_values, dcount_newvalues
| render timechart 
```

|_day|dcount_values|dcount_newvalues|
|---|---|---|
|2017-01-02 00:00:00.0000000|630|630|
|2017-01-09 00:00:00.0000000|738|575|
|2017-01-16 00:00:00.0000000|1187|841|
|2017-01-23 00:00:00.0000000|1092|465|
|2017-01-30 00:00:00.0000000|1261|647|
|2017-02-06 00:00:00.0000000|1744|1043|
|2017-02-13 00:00:00.0000000|1563|432|
|2017-02-20 00:00:00.0000000|1406|818|
|2017-02-27 00:00:00.0000000|1956|1429|
|2017-03-06 00:00:00.0000000|1593|848|
|2017-03-13 00:00:00.0000000|1801|1423|
|2017-03-20 00:00:00.0000000|1710|1017|
|2017-03-27 00:00:00.0000000|1796|1516|
|2017-04-03 00:00:00.0000000|1381|1008|
|2017-04-10 00:00:00.0000000|1756|1162|
|2017-04-17 00:00:00.0000000|1831|1409|
|2017-04-24 00:00:00.0000000|1823|1164|
|2017-05-01 00:00:00.0000000|1811|1353|
|2017-05-08 00:00:00.0000000|1691|1246|
|2017-05-15 00:00:00.0000000|1812|1608|
|2017-05-22 00:00:00.0000000|1740|1017|
|2017-05-29 00:00:00.0000000|960|756|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-dcount-and-dcount-newvalues.png" border="false" alt-text="作用指標 dcount 和計數新值":::
