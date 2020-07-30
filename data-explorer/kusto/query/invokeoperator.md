---
title: 叫用運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 invoke 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: debd0a83f4c27fa05415805404ad450bab1d3ca1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347369"
---
# <a name="invoke-operator"></a>invoke 運算子

`invoke`叫用接收做為表格式參數引數之來源的 lambda。

```kusto
T | invoke foo(param1, param2)
```

## <a name="syntax"></a>語法

`T | invoke`函式*function* `(`[*param1* `,`*param2*]`)`

## <a name="arguments"></a>引數

* *T*：表格式來源。
* *function*：要評估之 lambda 運算式或函式名稱的名稱。
* *param1*， *param2* ...：其他 lambda 引數。

## <a name="returns"></a>傳回

傳回已評估運算式的結果。

**備註**

如需詳細資訊，請參閱[let 語句](./letstatement.md)，以瞭解如何宣告可以接受表格式引數的 lambda 運算式。

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
