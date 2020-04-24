---
title: series_iir() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 azure 數據資源管理器中的series_iir()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 96452265e07a8a8b057dc70bec520be6ab298dd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508291"
---
# <a name="series_iir"></a>series_iir()

在序列上應用無限脈衝回應篩選器。  

以包含動態數值陣列的運算式作為輸入,並應用[無限脈衝回應](https://en.wikipedia.org/wiki/Infinite_impulse_response)篩選器。 通過指定濾波器係數,它可以用來計算序列的累積和數,應用平滑運算以及各種[高通](https://en.wikipedia.org/wiki/High-pass_filter)、[帶通](https://en.wikipedia.org/wiki/Band-pass_filter)[和低通](https://en.wikipedia.org/wiki/Low-pass_filter)濾波器。 該函數將包含動態數位的列和篩選器的*a*和*b*係數的兩個靜態動態數位作為輸入,並將篩選器應用於列。 它會輸出新的動態陣列資料行，其中包含已篩選的輸出。  
 

**語法**

`series_iir(`*x* `,` *b* `,` *a*`)`

**引數**

* *x*: 動態陣列儲存格,它是數值陣列,通常是[生成序列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算符的輸出。
* *b*: 包含篩選器的分子係數的常量運算式(儲存為數值的動態陣列)。
* *a*: 常數表示式,如*b*。 包含濾波器的分母係數。

> [!IMPORTANT]
> (即`a[0]``a`) 的第一個元素不能為零(以避免除以 0;請參閱下面的公式)。

**有關過濾器遞迴公式的更多**

* 給定一個輸入陣列 X 和係陣列 a、b 的長度n_a和n_b,則濾波器的傳輸函數(生成輸出陣列 Y)定義(另請參閱維琪百科):

<div align="center">
Y<sub>i</sub> =<sub>0</sub><sup>-1</sup>(b<sub>0</sub>X<sub>i</sub> 
 + b<sub>1</sub>X<sub>i-1</sub> + ... = b<sub>n<sub>b</sub>-1</sub>X<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>Y<sub>i-1</sub>-a<sub>2</sub>Y<sub>i-2</sub> - ... -<sub>n<sub>a</sub>-1</sub>Y<sub>i-n<sub>a</sub>-1</sub>)
</div>

**範例**

計算累積總和可以通過 iir 濾波器執行,其係數*為*[1,-1] 和*b*[1]:  

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

下面瞭解如何將其包裝在函數中:

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