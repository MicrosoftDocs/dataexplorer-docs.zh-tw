---
title: series_fill_forward （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_fill_forward （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7ea5210f0370b495c48615d28e763bf6e396d46e
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763700"
---
# <a name="series_fill_forward"></a>series_fill_forward()

執行數列中遺漏值的向前填滿插補。

包含動態數值陣列的運算式是輸入。 函式會將 missing_value_placeholder 的所有實例取代為 missing_value_placeholder 以外的最接近值，並傳回產生的陣列。 系統會保留最左邊的 missing_value_placeholder 實例。

**語法**

`series_fill_forward(`*x* `[, `*missing_value_placeholder*`])`
* 會傳回數列*x* ，其中包含所有*missing_value_placeholder*已向轉寄的實例。

**引數**

* *x*：動態陣列純量運算式，這是數值的陣列。 
* *missing_value_placeholder*：選擇性參數，指定要取代之遺漏值的預留位置。 預設值為 `double` （*null*）。

**備註**

* 指定*null*做為預設值，以在[進行數列](make-seriesoperator.md)之後套用插補函式： 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* *Missing_value_placeholder*可以是任何將轉換成實際元素類型的類型。 `double`（*Null*） `long` （*null*）和 `int` （*null*）都具有相同的意義。
* 如果 missing_value_placeholder 為（null）（或省略，其意義相同），則結果可能會包含*null*值。 若要填入這些*null*值，請使用其他插補函數。 目前只有[series_outliers （）](series-outliersfunction.md)支援輸入陣列中的*null*值。
* 函式會保留陣列元素的原始類型。

**範例**

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
|[null，null，36，41，null，null，16，61，33，null，null]|[null，null，36，41，41，41，16，61，33，33，33]|
   
使用[series_fill_backward](series-fill-backwardfunction.md)或[數列-填滿常數](series-fill-constfunction.md)來完成上述陣列的插補。
