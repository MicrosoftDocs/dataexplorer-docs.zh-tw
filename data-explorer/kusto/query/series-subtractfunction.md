---
title: 'series_subtract ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_subtract。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 09d208b25096afa58bd9f673d0103b30f7e8bb47
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241951"
---
# <a name="series_subtract"></a>series_subtract()

計算兩個數值數列輸入的元素成對減法。

## <a name="syntax"></a>語法

`series_subtract(`*series1* `,`*series2*`)`

## <a name="arguments"></a>引數

* *series1，series2*：輸入數值陣列，第二個會從第一次減去為動態陣列結果中的元素。 所有引數都必須是動態陣列。 

## <a name="returns"></a>傳回

這兩個輸入之間的匯出專案的雙向減法運算動態陣列。 任何非數值元素或非現有的元素 (不同大小的陣列，) 產生 `null` 元素值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1，2，4]|[4，2，1]|[-3，0，3]|
|[2，4，8]|[8，4，2]|[-6，0，6]|
|[3，6，12]|[12，6，3]|[-9，0，9]|
