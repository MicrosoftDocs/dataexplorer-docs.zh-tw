---
title: series_multiply （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_multiply （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 112fe4135b6ed996c798e5f03a34120e4078c623
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351381"
---
# <a name="series_multiply"></a>series_multiply()

計算兩個數值數列輸入的元素取向乘法。

## <a name="syntax"></a>語法

`series_multiply(`*series1* `,`*series2*`)`

## <a name="arguments"></a>引數

* *series1，series2*：輸入數值陣列，要以元素的乘積乘以動態陣列結果。 所有引數都必須是動態陣列。 

## <a name="returns"></a>傳回

這兩個輸入之間的計算元素取向乘法運算動態陣列。 任何非數值元素或不存在的專案（不同大小的陣列）都會產生 `null` 元素值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1，2，4]    |[4，2，1]|   [4，4，4]|
|[2，4，8]    |[8，4，2]|   [16，16，16]|
|[3，6，12]   |[12，6，3]|  [36，36，36]|
