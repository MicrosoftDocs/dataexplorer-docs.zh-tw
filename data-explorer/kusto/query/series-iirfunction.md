---
title: 'series_iir ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_iir。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 3cd0559393e9c5194b06bcf93449a68b7d55cc1c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250032"
---
# <a name="series_iir"></a>series_iir()

在數列上套用無限脈衝回應篩選。  

函式會採用包含動態數值陣列的運算式做為輸入，並套用 [無限脈衝回應](https://en.wikipedia.org/wiki/Infinite_impulse_response) 篩選。 藉由指定篩選係數，即可使用函數：
* 計算數列的累計總和
* 套用平滑作業
* 套用各種 [高階傳遞](https://en.wikipedia.org/wiki/High-pass_filter)、 [頻外](https://en.wikipedia.org/wiki/Band-pass_filter)和 [低傳遞](https://en.wikipedia.org/wiki/Low-pass_filter) 篩選

此函式會採用包含動態陣列的資料行，以及篩選準則 *a* 和 *b* 係數的兩個靜態動態陣列，來做為輸入，並在資料行上套用篩選。 它會輸出新的動態陣列資料行，其中包含已篩選的輸出。  

## <a name="syntax"></a>語法

`series_iir(`*x* `,` *b* `,` *a*`)`

## <a name="arguments"></a>引數

* *x*：動態陣列資料格，這是數值的陣列，通常是 [構成數列](make-seriesoperator.md) 或 [make_list](makelist-aggfunction.md) 運算子的結果輸出。
* *b*：常數運算式，其中包含篩選準則的分子係數 (儲存為數值的動態陣列) 。
* *答*：常數運算式，例如 *b*。 包含濾波器的分母係數。

> [!IMPORTANT]
>  (的第一個元素 `a` ， `a[0]`) 不得為零，以避免除以0。 請參閱 [下面的公式](#the-filters-recursive-formula)。

## <a name="the-filters-recursive-formula"></a>篩選的遞迴公式

* 請考慮輸入陣列 X，並將 a 和 b 的長度的係數分別 n_a 和 n_b。 將產生輸出陣列 Y 之篩選準則的傳送函式是由下列定義：

<div align="center">
Y<sub>i</sub> = a<sub>0</sub><sup>-1</sup> (b<sub>0</sub>X<sub>i</sub> 
 + b<sub>1</sub>X<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>X<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>Y<sub>i-1</sub>-a<sub>2</sub>Y<sub>i-2</sub> - ...-a<sub>n<sub>a</sub>-1</sub>y<sub>i-n<sub>a</sub>-1</sub>) 
</div>

## <a name="example"></a>範例

計算累計總和。 使用具有係數 *a*= [1，-1] 和 *b*= [1] 的 iir 篩選：  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let x = range(1.0, 10, 1);
print x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mv-expand x, y
```

| x | y |
|:--|:--|
|1.0|1.0|
|2.0|3.0|
|3.0|6.0|
|4.0|10.0|

以下是如何將其包裝在函式中：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let vector_sum=(x:dynamic)
{
  let y=array_length(x) - 1;
  toreal(series_iir(x, dynamic([1]), dynamic([1, -1]))[y])
};
print d=dynamic([0, 1, 2, 3, 4])
| extend dd=vector_sum(d)
```

|d            |dd  |
|-------------|----|
|`[0,1,2,3,4]`|`10`|
