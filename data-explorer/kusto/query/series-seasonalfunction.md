---
title: series_seasonal() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 series_seasonal()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 559731ab7dca2e49e124a856ca7a66a912583b62
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663139"
---
# <a name="series_seasonal"></a>series_seasonal()

根據檢測到的季節或給定的季節計算序列的季節性分量。

**語法**

`series_seasonal(`*系列*`[,`*週期*`])`

**引數**

* *欄*位 : 輸入數值動態陣列
* *期間*(可選):每個季節的條柱數整數,可能的值:
    *  -1(預設值):使用閾值*為 0.7*的[series_periods_detect()](series-periods-detectfunction.md)自動檢測週期,如果未檢測到季節性,則返回零
    * 正整數:將用作季節性成分的期間
    * 任何其他值:忽略季節性並傳回零

**傳回**

長度與包含序列計算的季節性分量的*序列*輸入長度相同的動態數位。 季節性元件計算為與各個期間的 bin 位置對應的值的*中位數*。

**另請參閱:**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**範例**

**1. 自動偵測週期**

在下面的示例中,序列週期將自動檢測到,第一個系列的周期檢測到 6 個柱,第二個 5 個條柱,第三個系列的週期太短,無法檢測到並返回一系列零(請參閱下一個有關如何強制週期的示例)。

```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1]|[1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0]|
|[8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]|[10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0]|



**2. 強制一個期間**

在下面的示例中,序列週期太短[,series_periods_detect()](series-periods-detectfunction.md)無法檢測到,因此我們顯式強制該週期獲取季節性模式。

```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1,3,5,1,3,5,2,4,6]|[1.0,3.0,5.0,1.0,3.0,5.0,1.0,3.0,5.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5]|
