---
title: 範圍運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的範圍運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1eddf7fe1e9121a134e222f6a19b9ebeede3a762
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243139"
---
# <a name="range-operator"></a>range 運算子

產生單一資料行的值資料表。

請注意它沒有管線輸入。 

## <a name="syntax"></a>語法

`range`*columnName* `from`*開始* `to`*停止* `step`*步驟*

## <a name="arguments"></a>引數

* *columnName*：輸出資料表中的單一資料行名稱。
* *start*：輸出中的最小值。
* *停止*：在輸出 (中產生的最大值，如果是此值的 *步驟* 步驟) ，則為最高值的系結。
* *步驟*：兩個連續值之間的差異。 

引數必須是數字、日期或時間範圍值。 引數不能參考任何資料表的資料行   (如果您要根據輸入資料表計算範圍，請使用 range 函式，或許是使用 mv 展開運算子。 )  

## <a name="returns"></a>傳回

具有單一資料行（名為 *columnName*）的資料表，其值為 *start*、 *start* `+` *step*、.。。最多至 *停止*。

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

下一個範例會示範如何 `range` 使用運算子來建立小型的臨機操作維度資料表，然後用它來導入零，而來源資料沒有任何值。

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
