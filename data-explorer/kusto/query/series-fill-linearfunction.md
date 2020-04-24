---
title: series_fill_linear() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 series_fill_linear()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f9a12d172a1580d6a9e575b78b14404dce7820e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508648"
---
# <a name="series_fill_linear"></a>series_fill_linear()

對序列中缺失值執行線性插值。

以包含動態數值陣列的運算式作為輸入,對所有missing_value_placeholder實例執行線性插值,並返回生成的陣列。 如果陣列的開頭和結尾包含missing_value_placeholder,則它將替換為missing_value_placeholder以外的最近值(可以關閉)。 如果整個陣列由missing_value_placeholder組成,則陣列將填充constant_value或 0(如果未指定)。  

**語法**

`series_fill_linear(`*x* `[,` x ` [,` ` [,` missing_value_placeholderfill_edgesconstant_value* * *fill_edges* * *`]]]))`
* 將使用指定的參數返回*x*的序列線性插值。
 

**引數**

* *x*: 動態陣列標量表示式,它是數值陣列。
* *missing_value_placeholder*:可選參數,該參數指定要替換的"缺失值"的佔位符。 預設值為`double`*(null)。*
* *fill_edges*: 布林值,指示是否應將陣列開頭和末尾*的missing_value_placeholder*替換為最近值。 預設情況下*為 true。* 如果設定為*false,* 則陣列開頭和末尾*的missing_value_placeholder*將保留。
* *constant_value*:僅與陣列相關的可選參數完全由*null*值組成,該值指定常量值以填充序列。 預設值為*0*。 將此參數設定為`double`(*null*) 將有效地將*空*值保留在原位置。

**注意事項**

* 為了在[製造序列](make-seriesoperator.md)後套用任何插值函數,建議將*null*指定為預設值: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *missing_value_placeholder*可以是任何類型的,將轉換為實際元素類型。 `double`因此, `long` *(null*) `int`(*null*) 或 (*null*) 具有相同的含義。
* 如果*missing_value_placeholder*missing_value_placeholder`double`為 (*null*) (或只是省略具有相同含義), 則結果可能包含*null*值。 使用其他插值函數來填充它們。 目前只有[series_outliers()](series-outliersfunction.md)支援輸入陣列中的*空*值。
* 函數保留原始類型的陣列元素。 如果*x*僅包含*int*或*長*元素,則線性插值將返回捨入的插值,而不是精確值。

**範例**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null, 111.0, null, 36.0, 41.0, null, null, 16.0, 61.0, 33.0, null, null]), // Array of double    
    dynamic([null, 111,   null, 36,   41,   null, null, 16,   61,   33,   null, null]), // Similar array of int
    dynamic([null, null, null, null])                                                   // Array with missing values only
];
data
| project arr, 
          without_args = series_fill_linear(arr),
          with_edges = series_fill_linear(arr, double(null), true),
          wo_edges = series_fill_linear(arr, double(null), false),
          with_const = series_fill_linear(arr, double(null), true, 3.14159)  

```

|阿爾爾|without_args|with_edges|wo_edges|with_const|
|---|---|---|---|---|
|[null,111.0,null,36.0,41.0,null,null,16.0,61.0,33.0,null, null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[null,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,null,null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|
|[null,111,null,36,41,null,null,16,61,33,null,null]|[111,111,73,36,41,32,24,16,61,33,33,33]|[111,111,73,36,41,32,24,16,61,33,33,33]|[null,111,73,36,41,32,24,16,61,33,null,null]|[111,111,74,38, 41,32,24,16,61,33,33,33]|
|[空,空,空,null]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[3.14159,3.14159,3.14159,3.14159]|