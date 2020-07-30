---
title: series_less_equals （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_less_equals （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: c758cc9a60e8e785031450e09e50e37b8fbf44b7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351415"
---
# <a name="series_less_equals"></a>series_less_equals()

計算兩個數值數列輸入的元素的小於或等於（ `<=` ）邏輯運算。

## <a name="syntax"></a>語法

`series_less_equals (`*Series1* `,`*Series2*`)`

## <a name="arguments"></a>引數

* *Series1，Series2*：輸入要比較元素的數值陣列。 所有引數都必須是動態陣列。 

## <a name="returns"></a>傳回

布林值的動態陣列，其中包含兩個輸入之間的計算元素較少或相等邏輯運算。 任何非數值元素或不存在的專案（不同大小的陣列）都會產生 `null` 元素值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_equals_s2 = series_less_equals(s1, s2)
```

|s1|s2|s1_less_equals_s2|
|---|---|---|
|[1，2，4]|[4，2，1]|[true，true，false]|

**另請參閱**

如需整個數列統計資料的比較，請參閱：
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
