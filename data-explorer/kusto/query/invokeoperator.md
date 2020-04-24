---
title: 呼叫運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的調用運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41f19440795f4f302352a8dda5192c5c4790ea99
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513697"
---
# <a name="invoke-operator"></a>invoke 運算子

呼叫接收作為表格參數參數參數的來源的`invoke`lambda。

```kusto
T | invoke foo(param1, param2)
```

**語法**

`T | invoke`*功能*`(`=*參數1* `,` *參數2*|`)`

**引數**

* *T*: 表格來源。
* *函數*:要計算的 lambda 運算式或函數名稱的名稱。
* *參數1,**第2段*...:額外的lambda參數。

**傳回**

返回計算表達式的結果。

**注意事項**

有關詳細資訊,請參閱[let 語句](./letstatement.md),瞭解如何聲明可以接受表格參數的 lambda 運算式。

**範例**

下面的範例展示如何使用`invoke`運算子呼叫 lambda 表示式:

```kusto
// clipped_average(): calculates percentiles limits, and then makes another 
//                    pass over the data to calculate average with values inside the percentiles
let clipped_average = (T:(x: long), lowPercentile:double, upPercentile:double)
{
   let high = toscalar(T | summarize percentiles(x, upPercentile));
   let low = toscalar(T | summarize percentiles(x, lowPercentile));
   T 
   | where x > low and x < high
   | summarize avg(x) 
};
range x from 1 to 100 step 1
| invoke clipped_average(5, 99)
```

|avg_x|
|---|
|52|
