---
title: series_stats() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_stats()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 07aa5df7351a5d4be1522d39456423197bde508d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507917"
---
# <a name="series_stats"></a>series_stats()

`series_stats()`在多列中返回序列的統計資訊。  

此`series_stats()`函式以包含動態數值陣列的欄做為輸入並計算以下欄:
* `min`:輸入陣列的最小值
* `min_idx`:輸入陣列最小值的第一個位置
* `max`:輸入陣列中的最大值
* `max_idx`:輸入陣列中最大值的第一個位置
* `avg`:輸入陣列的平均值
* `variance`:輸入陣列的樣本
* `stdev`:輸入陣列的樣本標準偏差

> [!NOTE] 
> 此函數返回多個列,因此不能將其用作另一個函數的參數。

**語法**

專案`series_stats(`*x* `[,` `series_stats(` *ignore_nonfinite*`])`或擴展*x*`)`返回上述所有列,名稱如下:series_stats_x_min、series_stats_x_min_idx等。
 
專案`series_stats(`(m, mi)=*x*`)`或擴展 (m,`series_stats(`mi)]*x*`)`傳回以下列: m (min) 和 mi (min_idx)。

**引數**

* *x*: 動態陣列儲存格,這是一個數值陣列。 
* *ignore_nonfinite*: 布林(選擇,`false`預設值 : ) 標誌,用於指定是否計算統計資訊,同時忽略非有限值(*空白*值 *、NaN*值 *、inf*等)。 如果設置為`false`,則返回的值`null`將是 數位中存在非有限值。

**範例**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32.8|28.5036338535483|812.457142857143|
