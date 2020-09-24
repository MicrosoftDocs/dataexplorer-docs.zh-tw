---
title: 'series_fill_const ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_fill_const。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8433773111f65e0271692bc3d1ba68cf0bc7c544
ms.sourcegitcommit: 44a4f7ea5c5d75301d7a09b7dc1254a1e5f08eaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210506"
---
# <a name="series_fill_const"></a>series_fill_const()

以指定的常數值取代數列中的遺漏值。

採用包含動態數值陣列的運算式做為輸入，以指定的 constant_value 取代 missing_value_placeholder 的所有實例，並傳回產生的陣列。

## <a name="syntax"></a>Syntax

`series_fill_const(`*x* `, `*constant_value* `[,`*missing_value_placeholder*`])`
* 會傳回數列 *x* ，其中所有的 *missing_value_placeholder* 實例都取代為 *constant_value*。

## <a name="arguments"></a>引數

* *x*：動態陣列純量運算式，這是數值的陣列。
* *constant_value*：取代遺漏值的值。 
* *missing_value_placeholder*：選擇性參數，指定要取代之遺漏值的預留位置。 預設值為 `double` *null*)  (。

**備註**
* 如果您使用「 [建立](make-seriesoperator.md) 數列」運算子來建立數列，則預設會填滿遺漏值0，或者您可以 `default = ` 在「建立數列」語句中指定 *DefaultValue* 來指定要填滿的常數值。

```kusto
make-series num=count() default=-1 on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* 若要在 [make 系列](make-seriesoperator.md)之後套用任何插補函數，請將 *null* 指定為預設值： 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* *Missing_value_placeholder*可以是任何型別，而這些型別會轉換成實際的元素類型。 如此一來， `double` (*null*) ， `long` (*null*) 或 `int` (*null*) 具有相同的意義。
* 函式會保留陣列元素的原始型別。 

## <a name="example"></a>範例

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
|[111，null，36，41，23，null，16，61，33，null，null]|[111、0.0、36、41、23、0.0、16、61、33、0.0、0.0]|[111，-1，36，41，23，-1，16，61，33，-1，-1]|
