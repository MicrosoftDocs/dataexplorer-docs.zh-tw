---
title: series_fill_backward （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_fill_backward （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f5ae36f00e7dc54b67eea5305c65a9ed4e44c572
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763408"
---
# <a name="series_fill_backward"></a>series_fill_backward()

執行數列中遺漏值的回溯填滿插補。

包含動態數值陣列的運算式是輸入。 函式會將 missing_value_placeholder 的所有實例取代為其右邊的最接近值（不是 missing_value_placeholder），並傳回產生的陣列。 系統會保留最右邊的 missing_value_placeholder 實例。

**語法**

`series_fill_backward(`*x* `[, `*missing_value_placeholder*`])`
* 會傳回數列*x* ，其中的所有實例*missing_value_placeholder*向後填滿。

**引數**

* *x*：動態陣列純量運算式，這是數值的陣列。
* *missing_value_placeholder*：這個選擇性參數會指定遺漏值的預留位置。 預設值為 `double` （*null*）。

**備註**

* 指定*null*做為預設值，以在[進行數列](make-seriesoperator.md)之後套用任何插補函式： 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* *Missing_value_placeholder*可以是任何將轉換成實際元素類型的類型。 `double`（*Null*）、 `long` （*null*）和 `int` （*null*）都具有相同的意義。
* 如果*missing_value_placeholder*為 `double` （*null*）、（或省略，其意義相同），則結果可能會包含*null*值。 若要填入這些*null*值，請使用其他插補函數。 目前只有[series_outliers （）](series-outliersfunction.md)支援輸入陣列中的*null*值。
* 函式會保留陣列元素的原始類型。

**範例**

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
|[111，null，36，41，null，null，16，61，33，null，null]|[111，36，36，41，16，16，16，61，33，null，null]|

  
使用[series_fill_forward](series-fill-forwardfunction.md)或[數列-填滿常數](series-fill-constfunction.md)來完成上述陣列的插補。
