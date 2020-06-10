---
title: series_fir （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_fir （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 616fee7b0a1b6852f66d3db22846b2645e03135f
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84665005"
---
# <a name="series_fir"></a>series_fir()

在數列上套用有限的脈衝回應（杉樹）篩選。  

函式會採用包含動態數值陣列做為輸入的運算式，並套用[有限的脈衝回應](https://en.wikipedia.org/wiki/Finite_impulse_response)篩選準則。 藉由指定 `filter` 係數，可以用來計算移動平均、凹凸貼圖、變更偵測，以及更多使用案例。 函式會採用包含動態陣列的資料行，以及篩選準則係數的靜態動態陣列做為輸入，並在資料行上套用篩選。 它會輸出新的動態陣列資料行，其中包含已篩選的輸出。  

**語法**

`series_fir(`*x* `,` *篩選*[正規化 `,` * *[ `,` *center*]]`)`

**引數**

* *x*：數值的動態陣列資料格。 [Make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出通常是。
* *filter*：常數運算式，其中包含篩選準則的係數（儲存為數值的動態陣列）。
* 正規化：選擇性的布林值，指出是否應將篩選*條件標準化。* 也就是除以係數的總和。 如果 filter 包含負值，*則必須將*正規化指定為 `false` ，否則結果會是 `null` 。 如果未指定，則會假設使用預設值正規化，這取決於*篩選準則*中*是否有負*數值。 如果*filter*至少包含一個負數值 *，則會*假設正規化為 `false` 。  
正規化是一個方便的方式，可確保係數的總和為1。 然後，篩選準則不會擴展或 attenuate 數列。 例如，由*filter*= [1，1，1，1] 和*正規化*= true 指定四個分類的移動平均，這比輸入 [0.25，0.25.0.25，0.25] 更容易。
* *center*：此為選擇性的布林值，指出是否要在目前點前後的時間範圍，或從目前點回溯的時間範圍，以對稱的來套用篩選。 根據預設，center 為 false，適用于串流資料的案例，我們只能在目前和較舊的點上套用篩選。 不過，針對臨機操作處理，您可以將它設定為 `true` ，讓它與時間序列保持同步。 請參閱下列範例。 這個參數會控制篩選的[群組延遲](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)。

**範例**

* 藉由設定*filter*= [1，1，1，1，1 *] 並正規化* = `true` （預設值），來計算五個點的移動平均。 請注意*center* = `false` （預設值）與下列各項的效果 `true` ：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

此查詢會傳回︰  
*5h_MovingAvg*：5個點移動平均篩選。 會將響應平滑化，且其高峰會移位 (5-1)/2 = 2 小時。  
*5h_MovingAvg_centered*：相同，但藉由設定 `center=true` ，尖峰會保留在其原始位置。

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="數列杉樹" border="false":::

* 若要計算某個點和其前一個點之間的差異，請設定*filter*= [1，-1]。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="數列杉樹2" border="false":::
