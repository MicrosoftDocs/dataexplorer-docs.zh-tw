---
title: series_seasonal （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_seasonal （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 88160a55ba8342e3ed6bce90ec77c5a370ab7358
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372477"
---
# <a name="series_seasonal"></a>series_seasonal()

根據偵測到或指定的季節性週期，計算數列的季節性元件。

**語法**

`series_seasonal(`*數列* `[,`*期間*`])`

**引數**

* *數列*：輸入數值動態陣列
* *period* （選擇性）：每個季節性期間中的每個區間的整數數目，可能的值：
    *  -1 （預設值）：使用[series_periods_detect （）](series-periods-detectfunction.md)的閾值*0.7*來自動偵測期間，如果未偵測到季節性則傳回零。
    * 正整數：將用來做為季節性元件的期間
    * 任何其他值：忽略季節性，並傳回一系列的零

**傳回**

與*數列*輸入相同長度的動態陣列，其中包含數列的計算季節性元件。 季節性元件的計算方式是將所有值對應到整個*期間的 bin*位置。

**另請參閱：**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**範例**

**1. 自動偵測期間**

在下列範例中，系統會自動偵測數列的期間，第一個數列的期間會被偵測為6個，而第二個數列則是第三個數列的時間長度太短而無法偵測，而且會傳回一系列的零（請參閱下一個有關如何強制執行此期間的範例）。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2，5，3，4，3，2，1，2，3，4，3，2，1，2，3，4，3，2，1，2，3，4，3，2，1]|[1.0，2.0，3.0，4.0，3.0，2.0，1.0，2.0，3.0，4.0，3.0，2.0，1.0，2.0，3.0，4.0，3.0，2.0，1.0，2.0，3.0，4.0，3.0，2.0，1.0]|
|[8、12、14、12、10、10、12、14、12、10、10、12、14、12、10、10、12、14、12、10]|[10.0，12.0，14.0，12.0，10.0，10.0，12.0，14.0，12.0，10.0，10.0，12.0，14.0，12.0，10.0，10.0，12.0，14.0，12.0，10.0]|
|[1，3，5，2，4，6，1，3，5，2，4，6]|[0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0]|



**2. 強制執行句點**

在下列範例中，序列的期間太短，無法由[series_periods_detect （）](series-periods-detectfunction.md)偵測到，因此我們會強制此期間明確地取得季節性模式。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1，3，5，1，3，5，2，4，6]|[1.0，3.0，5.0，1.0，3.0，5.0，1.0，3.0，5.0]|
|[1，3，5，2，4，6，1，3，5，2，4，6]|[1.5，3.5，5.5，1.5，3.5，5.5，1.5，3.5，5.5，1.5，3.5，5.5]|
