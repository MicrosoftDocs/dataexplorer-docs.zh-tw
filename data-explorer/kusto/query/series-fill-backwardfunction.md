---
title: 'series_fill_backward ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_fill_backward。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 572a80cc6e11a94f1597478f8395fe477f0cb64c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247218"
---
# <a name="series_fill_backward"></a>series_fill_backward()

在數列中執行遺漏值的後置填滿插補。

包含動態數值陣列的運算式是輸入。 函式會將 missing_value_placeholder 的所有實例取代為 missing_value_placeholder) 以外最接近的值 (，並傳回產生的陣列。 系統會保留最右邊的 missing_value_placeholder 實例。

## <a name="syntax"></a>語法

`series_fill_backward(`*x* `[, `*missing_value_placeholder*`])`
* 會傳回數列 *x* ，其中 *missing_value_placeholder* 的所有實例會向後填滿。

## <a name="arguments"></a>引數

* *x*：動態陣列純量運算式，這是數值的陣列。
* *missing_value_placeholder*：此選擇性參數會指定遺漏值的預留位置。 預設值為 `double` (*null*) 。

**備註**

* 將 *null* 指定為預設值，以在 [進行數列](make-seriesoperator.md)之後套用任何插補函式： 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* *Missing_value_placeholder*可以是將轉換成實際專案類型的任何類型。 `double` (*null*) 、 `long` (*null*) 和 `int` (*null*) 具有相同的意義。
* 如果 *missing_value_placeholder* `double` (*null*) ， (或省略，其意義相同) 那麼結果可能會包含 *null* 值。 若要填滿這些 *null* 值，請使用其他插補函數。 目前只有 [series_outliers ( # B1 ](series-outliersfunction.md) 支援輸入陣列中的 *null* 值。
* 函式會保留原始類型的陣列元素。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_backward(arr)

```

|`arr`|`fill_forward`|
|---|---|
|[111、null、36、41、null、null、16、61、33、null、null]|[111、36、36、41、16、16、16、61、33、null、null]|

  
使用 [series_fill_forward](series-fill-forwardfunction.md) 或 [數列填滿常數](series-fill-constfunction.md) 來完成上述陣列的內插補點。
