---
title: activity_metrics 外掛程式-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 activity_metrics 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8106d419f20dcacdec6386294a5b9ffb8d1bc8e2
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225899"
---
# <a name="activity_metrics-plugin"></a>activity_metrics plugin

根據目前的期間與上一個期間時間範圍，計算有用的活動計量（相異計數值、新值的相異計數、保留率和變換率）（不同于每個時間範圍與*所有*先前的時段比較的[activity_counts_metrics 外掛程式](activity-counts-metrics-plugin.md)）。

```kusto
T | evaluate activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` `activity_metrics(` *IdColumn* `,` *TimelineColumn* `,` [*開始* `,` *結束* `,` ]*視窗*[ `,` *dim1* `,` *dim2* `,` ...]`)`

**引數**

* *T*：輸入表格式運算式。
* *IdColumn*：識別碼值代表使用者活動的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *Start*：（選擇性）流量分析開始期間的值來進行純量。
* *End*：（選擇性）以 [分析結束期間] 的值為純量。
* *Window*：以 [分析] 視窗期間的值為純量。 可以是數值/日期時間/時間戳記值，或為其中一個的字串 `week` / `month` / `year` ，在此情況下，所有週期都會據以[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md) 。 
* *dim1*， *dim2*，...：（選擇性）分割活動度量計算的維度資料行清單。

**傳回**

傳回資料表，其中具有相異計數值、新值的相異計數、每個時間軸期間和每個現有維度組合的流失率。

輸出資料表架構為：

|*TimelineColumn*|dcount_values|dcount_newvalues|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|--|--|--|--|--|--|
|類型：從*TimelineColumn*|long|long|double|double|..|..|..|

**注意事項**

***保留率定義***

`Retention Rate`在一段期間內，會計算為：

    # of customers returned during the period
    / (divided by)
    # customers at the beginning of the period

其中 `# of customers returned during the period` 定義為：

    # of customers at end of period
    - (minus)
    # of new customers acquired during the period

`Retention Rate`可能會從0.0 變更為1。0  
較高的分數表示傳回的使用者數量較大。


***變換率定義***

`Churn Rate`在一段期間內，會計算為：
    
    # of customers lost in the period
    / (divided by)
    # of customers at the beginning of the period

其中 `# of customer lost in the period` 定義為：

    # of customers at the beginning of the period
    - (minus)
    # of customers at the end of the period

`Churn Rate`可能會從0.0 到1.0，分數越高，表示較大量的使用者不會返回服務。

***流失與保留率***

衍生自和的定義 `Churn Rate` `Retention Rate` ，下列一定是 true：

    [Retention rate] = 100.0% - [Churn Rate]


**範例**

### <a name="weekly-retention-rate-and-churn-rate"></a>每週保留率和變換率

下一個查詢會計算 [每週周數] 視窗的保留和流失率。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|2017-01-02 00：00：00.0000000|NaN|NaN|
|2017-01-09 00：00：00.0000000|0.179910044977511|0.820089955022489|
|2017-01-16 00：00：00.0000000|0.744374437443744|0.255625562556256|
|2017-01-23 00：00：00.0000000|0.612096774193548|0.387903225806452|
|2017-01-30 00：00：00.0000000|0.681141439205955|0.318858560794045|
|2017-02-06 00：00：00.0000000|0.278145695364238|0.721854304635762|
|2017-02-13 00：00：00.0000000|0.223172628304821|0.776827371695179|
|2017-02-20 00：00：00.0000000|0.38|0.62|
|2017-02-27 00：00：00.0000000|0.295519001701645|0.704480998298355|
|2017-03-06 00：00：00.0000000|0.280387770320656|0.719612229679344|
|2017-03-13 00：00：00.0000000|0.360628154795289|0.639371845204711|
|2017-03-20 00：00：00.0000000|0.288008028098344|0.711991971901656|
|2017-03-27 00：00：00.0000000|0.306134969325153|0.693865030674847|
|2017-04-03 00：00：00.0000000|0.356866537717602|0.643133462282398|
|2017-04-10 00：00：00.0000000|0.495098039215686|0.504901960784314|
|2017-04-17 00：00：00.0000000|0.198296836982968|0.801703163017032|
|2017-04-24 00：00：00.0000000|0.0618811881188119|0.938118811881188|
|2017-05-01 00：00：00.0000000|0.204657727593507|0.795342272406493|
|2017-05-08 00：00：00.0000000|0.517391304347826|0.482608695652174|
|2017-05-15 00：00：00.0000000|0.143667296786389|0.856332703213611|
|2017-05-22 00：00：00.0000000|0.199122325836533|0.800877674163467|
|2017-05-29 00：00：00.0000000|0.063468992248062|0.936531007751938|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-churn-and-retention.png" border="false" alt-text="活動計量流失和保留":::

### <a name="distinct-values-and-distinct-new-values"></a>相異值和相異的「新」值 

下一個查詢會計算 [每週星期] 視窗的相異值和「新」值（未顯示在上一個時間範圍內的識別碼）。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|2017-01-02 00：00：00.0000000|630|630|
|2017-01-09 00：00：00.0000000|738|575|
|2017-01-16 00：00：00.0000000|1187|841|
|2017-01-23 00：00：00.0000000|1092|465|
|2017-01-30 00：00：00.0000000|1261|647|
|2017-02-06 00：00：00.0000000|1744|1043|
|2017-02-13 00：00：00.0000000|1563|432|
|2017-02-20 00：00：00.0000000|1406|818|
|2017-02-27 00：00：00.0000000|1956|1429|
|2017-03-06 00：00：00.0000000|1593|848|
|2017-03-13 00：00：00.0000000|1801|1423|
|2017-03-20 00：00：00.0000000|1710|1017|
|2017-03-27 00：00：00.0000000|1796|1516|
|2017-04-03 00：00：00.0000000|1381|1008|
|2017-04-10 00：00：00.0000000|1756|1162|
|2017-04-17 00：00：00.0000000|1831|1409|
|2017-04-24 00：00：00.0000000|1823|1164|
|2017-05-01 00：00：00.0000000|1811|1353|
|2017-05-08 00：00：00.0000000|1691|1246|
|2017-05-15 00：00：00.0000000|1812|1608|
|2017-05-22 00：00：00.0000000|1740|1017|
|2017-05-29 00：00：00.0000000|960|756|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-dcount-and-dcount-newvalues.png" border="false" alt-text="活動計量的 dcount 和 dcount 新值":::
