---
title: 'series_fill_forward ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_fill_forward。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6aaa9a321792b7160d9a77ac74d3e5d650f67c6a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244711"
---
# <a name="series_fill_forward"></a>series_fill_forward()

在數列中執行遺漏值的向前填滿插補。

包含動態數值陣列的運算式是輸入。 函數會將 missing_value_placeholder 的所有實例取代為 missing_value_placeholder 左邊的最接近值，並傳回產生的陣列。 系統會保留最左邊的 missing_value_placeholder 實例。

## <a name="syntax"></a>語法

`series_fill_forward(`*x* `[, `*missing_value_placeholder*`])`
* 會傳回數列 *x* ，其中包含所有 *missing_value_placeholder* 填滿轉寄的實例。

## <a name="arguments"></a>引數

* *x*：動態陣列純量運算式，這是數值的陣列。 
* *missing_value_placeholder*：選擇性參數，指定要取代之遺漏值的預留位置。 預設值為 `double` *null*)  (。

**備註**

* 將 *null* 指定為預設值，以在 [進行系列](make-seriesoperator.md)之後套用插補函式： 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* *Missing_value_placeholder*可以是將轉換成實際專案類型的任何類型。 `double` (*null*) `long` (*null*) 和 `int` (*null*) 具有相同的意義。
* 如果 missing_value_placeholder (為 null)  (或省略，則會有相同的意義) ，則結果可能會包含 *null* 值。 若要填滿這些 *null* 值，請使用其他插補函數。 目前只有 [series_outliers ( # B1 ](series-outliersfunction.md) 支援輸入陣列中的 *null* 值。
* 這些函式會保留陣列元素的原始類型。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|`arr`|`fill_forward`|
|---|---|
|[null、null、36、41、null、null、16、61、33、null、null]|[null、null、36、41、41、41、16、61、33、33、33]|
   
使用 [series_fill_backward](series-fill-backwardfunction.md) 或 [數列填滿常數](series-fill-constfunction.md) 來完成上述陣列的內插補點。
