---
title: series_iir （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_iir （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 8b03970aacafef932f6397e64afdf871dc086bc1
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372620"
---
# <a name="series_iir"></a>series_iir()

在數列上套用無限脈衝回應篩選。  

採用包含動態數值陣列做為輸入的運算式，並套用[無限脈衝回應](https://en.wikipedia.org/wiki/Infinite_impulse_response)篩選準則。 藉由指定篩選係數，就可以使用它來計算數列的累計總和、套用平滑作業，以及各種[高階](https://en.wikipedia.org/wiki/High-pass_filter)、[頻外](https://en.wikipedia.org/wiki/Band-pass_filter)和[低](https://en.wikipedia.org/wiki/Low-pass_filter)行程篩選。 函式會採用包含動態陣列的資料行，以及篩選準則*a*和*b*係數的兩個靜態動態陣列做為輸入，並在資料行上套用篩選。 它會輸出新的動態陣列資料行，其中包含已篩選的輸出。  
 

**語法**

`series_iir(`*x* `,` *b* `,` *a*`)`

**引數**

* *x*：動態陣列資料格，這是數值的陣列，通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出。
* *b*：包含篩選之分子係數（儲存為數值的動態陣列）的常數運算式。
* *答*：常數運算式，例如*b*。 包含濾波器的分母係數。

> [!IMPORTANT]
> 的第一個元素 `a` （亦即 `a[0]` ）不得為零（以避免除數為 0; 請參閱下面的公式）。

**深入瞭解篩選的遞迴公式**

* 假設輸入陣列 X 和係數陣列 a、b 的長度分別 n_a 和 n_b，則產生輸出陣列 Y 的篩選準則傳送函式是由定義（另請參閱維琪百科）：

<div align="center">
Y<sub>i</sub> = a<sub>0</sub><sup>-1</sup>（b<sub>0</sub>x<sub>i</sub> 
 + b<sub>1</sub>x<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>x<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>Y i-<sub>1</sub>-a<sub>2</sub>Y<sub>i-2</sub> - ...-a<sub>n<sub>a</sub>-1</sub>Y<sub>i-n<sub>a</sub>-1</sub>）
</div>

**範例**

計算累計總和可以透過具有係數*a*= [1，-1] 和*b*= [1] 的 iir 濾波器篩選來執行：  

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

以下是將它包裝在函式中的方法：

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
