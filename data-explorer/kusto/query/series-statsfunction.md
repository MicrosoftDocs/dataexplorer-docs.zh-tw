---
title: 'series_stats ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_stats。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 5eb6bb2d11b35a7844a0366fc10797db621f6120
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241968"
---
# <a name="series_stats"></a>series_stats()

`series_stats()` 傳回多個資料行中數列的統計資料。  

`series_stats()`函數會採用包含動態數值陣列的資料行作為輸入，並計算下列資料行：
* `min`：輸入陣列中的最小值
* `min_idx`：輸入陣列中最小值的第一個位置
* `max`：輸入陣列中的最大值
* `max_idx`：輸入陣列中最大值的第一個位置
* `avg`：輸入陣列的平均值
* `variance`：輸入陣列的樣本差異
* `stdev`：輸入陣列的範例標準差

> [!NOTE] 
> 此函式會傳回多個資料行，因此不能當做另一個函式的引數使用。

## <a name="syntax"></a>語法

project `series_stats(` *x* `[,` *ignore_nonfinite* `])` 或 extend `series_stats(` *x*會傳回 `)` 所有上面提及的資料行，這些資料行具有下列名稱： series_stats_x_min、series_stats_x_min_idx 等等。
 
專案 (m、mi) = `series_stats(` *x* `)` 或擴充 (m、mi) = `series_stats(` *x*會傳回 `)` 下列資料行： m (min) 和 mi (min_idx) 。

## <a name="arguments"></a>引數

* *x*：動態陣列資料格，這是數值的陣列。 
* *ignore_nonfinite*：布林值 (選擇性，預設值： `false`) 旗標，指定是否要在略過非有限值 (*null*、 *NaN*、 *inf*等 ) 時計算統計資料。 如果設定為 `false` ，則傳回的值會是 `null` 陣列中是否有非有限值的值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32.8|28.5036338535483|812.457142857143|
