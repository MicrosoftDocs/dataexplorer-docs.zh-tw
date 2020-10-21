---
title: 'series_divide ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_divide。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 093f78cfc30da31474094187a786585fdcd5132b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252908"
---
# <a name="series_divide"></a>series_divide()

計算兩個數值數列輸入的元素面向除法。

## <a name="syntax"></a>語法

`series_divide(`*series1* `,`*series2*`)`

## <a name="arguments"></a>引數

* *series1，series2*：輸入數值陣列，第一個是元素取向的第二個，成為動態陣列結果。 所有引數都必須是動態陣列。 

## <a name="returns"></a>傳回

這兩個輸入之間的計算專案的動態陣列運算。 任何非數值元素或非現有的元素 (不同大小的陣列，) 產生 `null` 元素值。

注意：結果序列是 double 類型，即使輸入是整數也是一樣。 零除會以零零除的 (，例如2/0 會產生雙 (+ inf) # A3。

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
