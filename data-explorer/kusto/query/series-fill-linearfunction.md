---
title: series_fill_linear （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_fill_linear （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4ef02ab79b0701b4af74744a94e0ff795eb8c26a
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737244"
---
# <a name="series_fill_linear"></a>series_fill_linear()

線性插補數列中的遺漏值。

採用包含動態數值陣列做為輸入的運算式，針對 missing_value_placeholder 的所有實例執行線性插補，並傳回產生的陣列。 如果陣列的開頭和結尾包含 missing_value_placeholder，則會使用 missing_value_placeholder 以外的最接近值來取代它。 這項功能可以關閉。 如果整個陣列是由 missing_value_placeholder 所組成，陣列將會填入 constant_value，如果未指定，則為0。  

**語法**

`series_fill_linear(`*x* `[,` * *missing_value_placeholder` [,`*fill_edges*fill_edges` [,`*constant_value*`]]]))`
* 會使用指定的參數傳回*x*的數列線性插補。
 

**引數**

* *x*：動態陣列純量運算式，這是數值的陣列。
* *missing_value_placeholder*：選擇性參數，指定要取代的「遺漏值」的預留位置。 預設值為`double`（*null*）。
* *fill_edges*：布林值，指出是否應該以最接近的值取代陣列開頭和結尾的*missing_value_placeholder* 。 預設值*為 True* 。 如果設定為*false*，則會保留陣列開頭和結尾*missing_value_placeholder* 。
* *constant_value*：僅與陣列相關的選擇性參數，完全由*null*值組成。 這個參數會指定要用來填滿數列的常數值。 預設值為*0*。 將這個參數設定為`double`（*null*），會有效地將*null*值保留在其中。

**注意事項**

* 若要在[make 系列](make-seriesoperator.md)之後套用任何插補函式，請將*null*指定為預設值： 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *Missing_value_placeholder*可以是任何將轉換成實際元素類型的類型。 因此， `double`（*null*）、 `long`（*null*）或`int`（*null*）具有相同的意義。
* 如果*missing_value_placeholder*為`double`（*null*）（或省略，其意義相同），則結果可能會包含*null*值。 使用其他插補函式來填滿這些*null*值。 目前只有[series_outliers （）](series-outliersfunction.md)支援輸入陣列中的*null*值。
* 函式會保留陣列元素的原始類型。 如果 x 只包含 int 或 long 元素，則線性插補會傳回四捨五入的插補值，而不是精確的。

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

|`arr`|`without_args`|`with_edges`|`wo_edges`|`with_const`|
|---|---|---|---|---|
|[null，111.0，null，36.0，41.0，null，null，16.0，61.0，33.0，null，null]|[111.0、111.0、73.5、36.0、41.0、32.667、24.333、16.0、61.0、33.0、33.0、33.0]|[111.0、111.0、73.5、36.0、41.0、32.667、24.333、16.0、61.0、33.0、33.0、33.0]|[null，111.0，73.5，36.0，41.0，32.667，24.333，16.0，61.0，33.0，null，null]|[111.0、111.0、73.5、36.0、41.0、32.667、24.333、16.0、61.0、33.0、33.0、33.0]|
|[null，111，null，36，41，null，null，16，61，33，null，null]|[111111，73，36，41，32，24，16，61，33，33，33]|[111111，73，36，41，32，24，16，61，33，33，33]|[null，111，73，36，41，32，24，16，61，33，null，null]|[111111，74，38，41，32，24，16，61，33，33，33]|
|[null，null，null，null]|[0.0，0.0，0.0，0.0]|[0.0，0.0，0.0，0.0]|[0.0，0.0，0.0，0.0]|[3.14159，3.14159，3.14159，3.14159]|