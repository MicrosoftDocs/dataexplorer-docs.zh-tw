---
title: series_divide （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_divide （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 39252fb8e7233ddc3532003afc7a131505cd4282
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345074"
---
# <a name="series_divide"></a>series_divide()

計算兩個數值數列輸入的元素成對除法。

## <a name="syntax"></a>語法

`series_divide(`*series1* `,`*series2*`)`

## <a name="arguments"></a>引數

* *series1，series2*：輸入數值陣列，第一個是以元素的方式除以第二個，成為動態陣列結果。 所有引數都必須是動態陣列。 

## <a name="returns"></a>傳回

這兩個輸入之間的計算元素取向運算動態陣列。 任何非數值元素或不存在的專案（不同大小的陣列）都會產生 `null` 元素值。

注意：結果數列是 double 型別，即使輸入是整數也一樣。 零除會沿著零除（例如2/0 產生雙精度（+ inf））。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1，2，4]    |[4，2，1]|   [0.25，1.0，4.0]|
|[2，4，8]    |[8，4，2]|   [0.25，1.0，4.0]|
|[3，6，12]   |[12，6，3]|  [0.25，1.0，4.0]|
