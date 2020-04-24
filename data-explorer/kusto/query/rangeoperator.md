---
title: 範圍運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的範圍運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7ec559a23e28568ce7abd8365cdc502ad05a9b0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510603"
---
# <a name="range-operator"></a>range 運算子

產生單一資料行的值資料表。

請注意它沒有管線輸入。 

**語法**

`range`*列名稱*`from`*開始*`to`*停止*`step`*步驟*

**引數**

* *列名稱*:輸出表中單列的名稱。
* *起始*值 :輸出中的最小值。
* *停止*:在輸出中生成的最高值(或對最大值的綁定,如果*步長*超過此值)。
* *步驟*:兩個連續值之間的差異。 

引數必須是數字、日期或時間範圍值。 引數不能參考任何資料表的資料行  (如果要基於輸入表計算範圍,請使用範圍函數,可能使用 mv-展開運算符。 

**傳回**

具有單列稱為*列名稱*的表,其值為 *「開始*」*, 起始*`+`*步驟*, ...直到*停止*。

**範例**  

過去七天的午夜資料表。 bin(floor) 函式會逐次減少至一天的開始。

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

```kusto
range Steps from 1 to 8 step 3
```

下一個範例展示如何使用`range`運算符創建一個小型的、臨時的維度表,然後用於在源數據沒有值的地方引入零。

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