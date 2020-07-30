---
title: 範圍運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的範圍運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5c736492745d47428b5919d9791aa6115aaf8566
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345890"
---
# <a name="range-operator"></a>range 運算子

產生單一資料行的值資料表。

請注意它沒有管線輸入。 

## <a name="syntax"></a>語法

`range`*columnName* `from`*啟動* `to`*停止* `step`*步驟*

## <a name="arguments"></a>引數

* *columnName*：輸出資料表中的單一資料行名稱。
* *start*：輸出中的最小值。
* *stop*：在輸出中產生的最大值（如果*步驟*不超過此值，則為最大值的系結）。
* *步驟*：兩個連續值之間的差異。 

引數必須是數字、日期或時間範圍值。 引數不能參考任何資料表的資料行  （如果您想要根據輸入資料表計算範圍，請使用 range 函式，也許是透過 mv-expand 運算子）。 

## <a name="returns"></a>傳回

具有名為*columnName*之單一資料行的資料表，其值為*start*、 *start* `+` *step*、.。。直到*停止*為止。

## <a name="example"></a>範例  

過去七天的午夜資料表。 bin(floor) 函式會逐次減少至一天的開始。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range LastWeek from ago(7d) to now() step 1d
```

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|


具有名稱為 `Steps` 之單一資料行的資料表，其類型為 `long`，其值則為 `1`、`4` 和 `7`。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range Steps from 1 to 8 step 3
```

下一個範例示範如何 `range` 使用運算子來建立小型的特定維度資料表，然後用它來導入零，其中來源資料沒有任何值。

```kusto
range TIMESTAMP from ago(4h) to now() step 1m
| join kind=fullouter
  (Traces
      | where TIMESTAMP > ago(4h)
      | summarize Count=count() by bin(TIMESTAMP, 1m)
  ) on TIMESTAMP
| project Count=iff(isnull(Count), 0, Count), TIMESTAMP
| render timechart  
```
