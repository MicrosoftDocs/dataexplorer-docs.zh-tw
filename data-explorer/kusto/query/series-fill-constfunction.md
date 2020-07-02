---
title: series_fill_const （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_fill_const （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e078919af16a9d2f7dadba0a309932b3a39b6ced
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763252"
---
# <a name="series_fill_const"></a>series_fill_const()

以指定的常數值取代數列中的遺漏值。

採用包含動態數值陣列做為輸入的運算式，以指定的 constant_value 取代 missing_value_placeholder 的所有實例，並傳回產生的陣列。

**語法**

`series_fill_const(`*x* `[, `*constant_value* `[,`*missing_value_placeholder*`]])`
* 會傳回數列*x* ，其中的所有實例*missing_value_placeholder*都會取代為*constant_value*。

**引數**

* *x*：動態陣列純量運算式，這是數值的陣列。
* *constant_value*：參數，指定要取代之遺漏值的預留位置。 預設值為*0*。 
* *missing_value_placeholder*：選擇性參數，指定要取代之遺漏值的預留位置。 預設值為 `double` （*null*）。

**備註**
* 您可以使用 DefaultValue 語法來建立填滿常數值的數列 `default = ` *DefaultValue* （或只省略會假設為0的）。 如需詳細資訊，請參閱[make 系列](make-seriesoperator.md)。

```kusto
make-series num=count() default=-1 on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* 若要在[make 系列](make-seriesoperator.md)之後套用任何插補函式，請將*null*指定為預設值： 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* *Missing_value_placeholder*可以是任何類型，這會轉換成實際的元素類型。 因此， `double` （*null*）、 `long` （*null*）或 `int` （*null*）具有相同的意義。
* 函式會保留陣列元素的原始類型。 

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(`arr`: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|`arr`|`fill_const1`|`fill_const2`|
|---|---|---|
|[111，null，36，41，23，null，16，61，33，null，null]|[111，0.0，36，41，23，0.0，16，61，33，0.0，0.0]|[111，-1，36，41，23，-1，16，61，33，-1，-1]|
