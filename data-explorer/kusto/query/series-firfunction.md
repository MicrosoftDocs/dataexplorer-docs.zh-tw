---
title: series_fir （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 series_fir （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 4641da6481365d13919de59708387b689581c9ce
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618784"
---
# <a name="series_fir"></a>series_fir()

在數列上套用有限的脈衝回應篩選準則。  

採用包含動態數值陣列做為輸入的運算式，並套用[有限的脈衝回應](https://en.wikipedia.org/wiki/Finite_impulse_response)篩選準則。 藉由指定`filter`係數，可以用來計算移動平均、凹凸貼圖、變更偵測，以及更多使用案例。 此函式會採用包含濾波器係數的動態陣列和靜態動態陣列之資料行作為輸入，並且會套用資料行上的篩選條件。 它會輸出新的動態陣列資料行，其中包含已篩選的輸出。  

**語法**

`series_fir(`*x* `,` *篩選*[`,` *正規化*[`,` *center*]]`)`

**引數**

* *x*：動態陣列資料格，這是數值的陣列，通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出。
* *filter*：常數運算式，其中包含篩選準則的係數（儲存為數值的動態陣列）。
* 正規化 *：選擇性*的布林值，指出是否應將篩選準則正規化（也就是除以係數的總和）。 如果*filter*包含負值 *，則必須*將正規化指定為`false`，否則結果會是`null`。 如果未指定，則會假設預設*值為正規化*，視*篩選*中的負值值而定：如果*filter*包含至少一個負值 *，則會假設為正規化* `false`。  
正規化是一個方便的方式，可確保係數的總和為1，因此，篩選準則不會放大或濾波器數列。 例如， *filter*= [1，1，1，1] 和*正規化*= true 可指定4個 bin 的移動平均，這比輸入 [0.25，0.25.0.25，0.25] 更容易。
* *center*：選擇性的布林值，指出是否要在目前點前後的時間範圍，或從目前點回溯的時間範圍上，以對稱的來套用篩選。 依預設，置中為 false，適用於串流資料的案例，其中我們只可將濾波器套用在目前點和較舊的點；不過，針對臨機操作處理，您可將它設為 true，讓它與時間序列保持同步 (請參閱以下範例)。 就技術上來說，此參數會控制篩選器的[群組延遲](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)。

**範例**

* 您可以藉由設定*filter*= [1，1，1，1，1 *] 並正規化*= `true` （預設值），來計算5點的移動平均。 請注意*center* = `false` （預設值）與`true`下列各項的效果：

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
*5h_MovingAvg_centered*：相同但設定 center = true，會導致尖峰保留在其原始位置。

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="數列杉樹" border="false":::

* 藉由設定*filter*= [1，-1]，即可計算某個點和其前一個位置之間的差異：

```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="數列杉樹2" border="false":::
