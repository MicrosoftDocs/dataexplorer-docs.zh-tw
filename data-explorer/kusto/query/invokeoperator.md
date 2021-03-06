---
title: invoke 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 invoke 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1114952cdafe04284e93815d11c160455416b87c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248966"
---
# <a name="invoke-operator"></a>invoke 運算子

`invoke`叫用接收 as 表格式參數引數之來源的 lambda。

```kusto
T | invoke foo(param1, param2)
```

> [!NOTE]
> 如需如何宣告可接受表格式引數之 lambda 運算式的詳細資訊，請參閱 [let 語句](./letstatement.md) 。
 
## <a name="syntax"></a>語法

`T | invoke`*函數* `(`[*param1* `,`*param2*]`)`

## <a name="arguments"></a>引數

* *T*：表格式來源。
* *function*：要評估的 lambda 運算式或函式名稱。
* *param1*， *param2* ...：其他 lambda 引數。

## <a name="returns"></a>傳回

傳回評估運算式的結果。

## <a name="example"></a>範例

下列範例顯示如何使用 `invoke` 運算子來呼叫 lambda 運算式：

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
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
