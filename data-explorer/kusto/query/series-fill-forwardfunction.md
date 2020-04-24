---
title: series_fill_forward() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 series_fill_forward()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6c79733aa1abf001f52eb07606c866904e370906
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508665"
---
# <a name="series_fill_forward"></a>series_fill_forward()

對系列中缺失值執行正向填充插值。

以包含動態數值陣列的運算式作為輸入,將missing_value_placeholder的所有實例替換為來自其左側的最接近的值,而不是missing_value_placeholder並返回生成的陣列。 將保留missing_value_placeholder的最左側實例。

**語法**

`series_fill_forward(`*x*`[, `*missing_value_placeholder*`])`
* 將返回*x*系列,其中missing_value_placeholder*填充轉發*的所有實例。

**引數**

* *x*: 動態陣列標量表示式,它是數值陣列。 
* *missing_value_placeholder*:可選參數,該參數指定要替換的缺失值的佔位符。 預設值為`double`*(null)。*

**注意事項**

* 為了在[製造序列](make-seriesoperator.md)後套用任何插值函數,建議將*null*指定為預設值: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *missing_value_placeholder*可以是任何類型的,將轉換為實際元素類型。 `double`因此, `long` *(null*) `int`(*null*) 或 (*null*) 具有相同的含義。
* 如果*missing_value_placeholder*missing_value_placeholder`double`為 (*null*) (或只是省略具有相同含義), 則結果可能包含*null*值。 使用其他插值函數來填充它們。 目前只有[series_outliers()](series-outliersfunction.md)支援輸入陣列中的*空*值。
* 這些函數保留原始類型的陣列元素。

**範例**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|阿爾爾|fill_forward|
|---|---|
|[空,空,36,41,null,null,16,61,33,null,null]|[空,空,36,41,41,16,61,33,33,33]|
   
可以使用[series_fill_backward](series-fill-backwardfunction.md)或[序列填充 const](series-fill-constfunction.md)來完成上述陣列的插值。