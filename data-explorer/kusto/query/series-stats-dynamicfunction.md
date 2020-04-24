---
title: series_stats_dynamic() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_stats_dynamic()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: b667af6d037b0b316bf18406e1fb49528e390213
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507900"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

返回動態物件中序列的統計資訊。  

此`series_stats_dynamic()`函式以包含動態數值陣列的欄作為輸入,並產生包含以下內容的動態值:
* `min`:輸入陣列的最小值
* `min_idx`:輸入陣列最小值的第一個位置
* `max`:輸入陣列中的最大值
* `max_idx`:輸入陣列中最大值的第一個位置
* `avg`:輸入陣列的平均值
* `variance`:輸入陣列的樣本
* `stdev`:輸入陣列的樣本標準偏差

**語法**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite*`])`

**引數**

* *x*: 動態陣列儲存格,它是數值陣列。 
* *ignore_nonfinite*: 布林(選擇,`false`預設值 : ) 標誌,用於指定是否計算統計資訊,同時忽略非有限值(*空白*值 *、NaN*值 *、inf*等)。 如果設置為`false`返回的結果,則`null`陣列中存在非有限值。

**範例**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{"min":2.0,"min_idx":8,"最大值":87.0,"max_idx":3,"平均":32.8,"stdev":28.503633853548269,"方差":812.4571428514291 |