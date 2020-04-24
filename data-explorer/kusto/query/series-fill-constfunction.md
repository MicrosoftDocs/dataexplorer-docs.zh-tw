---
title: series_fill_const() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_fill_const()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7f5f939068737bdd6519fc0c63b663d19ae076a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508767"
---
# <a name="series_fill_const"></a>series_fill_const()

將序列中缺少的值替換為指定的常量值。

以包含動態數值陣列的運算式作為輸入,用指定的constant_value替換missing_value_placeholder的所有實例並返回生成的陣列。

**語法**

`series_fill_const(`*x*`[, `*constant_value* `[,` constant_valuemissing_value_placeholder *missing_value_placeholder*`]])`
* 將傳回*x*系列,所有*missing_value_placeholder*實體都取代為*constant_value*。

**引數**

* *x*: 動態陣列標量表示式,它是數值陣列。
* *constant_value*: 參數,該參數指定要取代的缺失值的占位符。 預設值為*0*。 
* *missing_value_placeholder*:可選參數,該參數指定要替換的缺失值的佔位符。 預設值為`double`*(null)。*

**注意事項**
* 可以使用`default = `*DefaultValue*語法(或僅省略將假定為 0)創建具有常量填充的序列。 有關詳細資訊[,請參閱製作系列](make-seriesoperator.md)。

```kusto
make-series num=count() default=-1 on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* 為了在[製造序列](make-seriesoperator.md)後套用任何插值函數,建議將*null*指定為預設值: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* *missing_value_placeholder*可以是任何類型的,將轉換為實際元素類型。 `double`因此, `long` *(null*) `int`(*null*) 或 (*null*) 具有相同的含義。
* 函數保留陣列元素的原始類型。 

**範例**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|阿爾爾|fill_const1|fill_const2|
|---|---|---|
|[111,null,36,41,23,null,16,61,33,null,null]|[111,0.0,36,41,23,0.0,16,61,33,0.0,0.0]|[111,-1,36,41,23,-1,16,61,33,-1,-1]|