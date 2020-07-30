---
title: series_add （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_add （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7b6de7d141f02703c5f369dd831d1fbac82cb45e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345516"
---
# <a name="series_add"></a>series_add()

計算兩個數值數列輸入的元素取向相加。

## <a name="syntax"></a>語法

`series_add(`*series1* `,`*series2*`)`

## <a name="arguments"></a>引數

* *series1，series2*：輸入數值陣列，使其成為動態陣列結果中已加入的元素。 所有引數都必須是動態陣列。 

## <a name="returns"></a>傳回

這兩個輸入之間的計算元素取向相加運算動態陣列。 任何非數值元素或不存在的專案（不同大小的陣列）都會產生 `null` 元素值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1，2，4]|[4，2，1]|[5，4，5]|
|[2，4，8]|[8，4，2]|[10，8，10]|
|[3，6，12]|[12，6，3]|[15，12，15]|
