---
title: 'series_fill_linear ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_fill_linear。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 01a2e5dfee4f68a0a5aee55946960e8cc0fd25cd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250783"
---
# <a name="series_fill_linear"></a>series_fill_linear()

以線性方式將數列中的遺漏值插補。

採用包含動態數值陣列的運算式做為輸入、對 missing_value_placeholder 的所有實例執行線性插補，並傳回產生的陣列。 如果陣列的開頭和結尾包含 missing_value_placeholder，則會將它取代為 missing_value_placeholder 以外的最接近值。 這項功能可以關閉。 如果整個陣列是由 missing_value_placeholder 所組成，則會以 constant_value 填滿陣列，如果未指定則為0。  

## <a name="syntax"></a>語法

`series_fill_linear(`*x* `[,` *missing_value_placeholder* ` [,` *fill_edges* ` [,` *constant_value*`]]]))`
* 將會使用指定的參數傳回 *x* 的序列線性插補。
 

## <a name="arguments"></a>引數

* *x*：動態陣列純量運算式，這是數值的陣列。
* *missing_value_placeholder*：選擇性參數，指定要取代之「遺漏值」的預留位置。 預設值為 `double` *null*)  (。
* *fill_edges*：布林值，指出是否應該以最接近的值取代陣列開頭和結尾的 *missing_value_placeholder* 。 預設*為 True* 。 如果設定為 *false*，則會保留陣列開頭和結尾的 *missing_value_placeholder* 。
* *constant_value*：僅與陣列完全相關的選擇性參數包含 *null* 值。 此參數會指定要用來填滿數列的常數值。 預設值為 *0*。 將此參數設定為 `double` (*Null*) 將會有效地保留 *null* 值。

## <a name="notes"></a>注意

* 若要在 [make 系列](make-seriesoperator.md)之後套用任何插補函數，請將 *null* 指定為預設值： 

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
    ```

* *Missing_value_placeholder*可以是將轉換成實際專案類型的任何類型。 如此一來， `double` (*null*) ， `long` (*null*) 或 `int` (*null*) 具有相同的意義。
* 如果 *missing_value_placeholder* `double` (*null*)  (或省略) ，則結果可能會包含 *null* 值。 使用其他插補函數來填滿這些 *null* 值。 目前只有 [series_outliers ( # B1 ](series-outliersfunction.md) 支援輸入陣列中的 *null* 值。
* 函式會保留原始類型的陣列元素。 如果 x 只包含 int 或 long 元素，則線性插補會傳回舍入的值，而非精確的值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|[null、111.0、null、36.0、41.0、null、null、16.0、61.0、33.0、null、null]|[111.0，111.0，73.5，36.0，41.0，32.667，24.333，16.0，61.0，33.0，33.0，33.0]|[111.0，111.0，73.5，36.0，41.0，32.667，24.333，16.0，61.0，33.0，33.0，33.0]|[null、111.0、73.5、36.0、41.0、32.667、24.333、16.0、61.0、33.0、null、null]|[111.0，111.0，73.5，36.0，41.0，32.667，24.333，16.0，61.0，33.0，33.0，33.0]|
|[null、111、null、36、41、null、null、16、61、33、null、null]|[111111、73、36、41、32、24、16、61、33、33、33]|[111111、73、36、41、32、24、16、61、33、33、33]|[null、111、73、36、41、32、24、16、61、33、null、null]|[111111、74、38、41、32、24、16、61、33、33、33]|
|[null，null，null，null]|[0.0，0.0，0.0，0.0]|[0.0，0.0，0.0，0.0]|[0.0，0.0，0.0，0.0]|[3.14159、3.14159、3.14159、3.14159]|
