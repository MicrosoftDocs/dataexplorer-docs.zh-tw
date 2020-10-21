---
title: 'series_fir ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_fir。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 8085455616fc97337ca115c1ef5b0c0e2422e08e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246190"
---
# <a name="series_fir"></a>series_fir()

針對數列 (的杉樹) 篩選套用有限的脈衝回應。  

函數會採用包含動態數值陣列作為輸入的運算式，並套用 [有限的脈衝回應](https://en.wikipedia.org/wiki/Finite_impulse_response) 篩選。 藉由指定 `filter` 係數，它可以用來計算移動平均、平滑化、變更偵測，以及其他更多使用案例。 函數會採用包含動態陣列的資料行，以及篩選係數的靜態動態陣列做為輸入，並在資料行上套用篩選。 它會輸出新的動態陣列資料行，其中包含已篩選的輸出。  

## <a name="syntax"></a>語法

`series_fir(`*x* `,` *篩選*[正規化 `,` * *[ `,` *center*]]`)`

## <a name="arguments"></a>引數

* *x*：數值的動態陣列資料格。 通常是 [make 系列](make-seriesoperator.md) 或 [make_list](makelist-aggfunction.md) 運算子產生的輸出。
* *篩選*：常數運算式，其中包含篩選準則的係數 (儲存為數值的動態陣列) 。
* 正規化 *：選擇性*的布林值，指出是否應該正規化篩選。 亦即除以係數的總和。 如果篩選準則包含負數值， *則必須* 將正規化指定為 `false` ，否則結果將會是 `null` 。 如果未指定，則會假設為預設值正規化，視*篩選準則*中是否存在負*數值而定*。 如果 *篩選* 至少包含一個負數值 *，則會假設為* `false` 。  
正規化是一種方便的方式，可確保係數的總和為1。 然後，篩選不會增強或 attenuate 數列。 例如， *篩選*= [1，1，1，1] 和 *正規化*= true 可以指定四個 bin 的移動平均，這比輸入 [0.25，0.25.0.25，0.25] 更容易。
* *置*中：選擇性的布林值，這個值會指出是否要在目前點之前和之後的時間範圍，或從目前點回溯的時間範圍，以對稱的情況套用篩選。 根據預設，center 為 false，可符合串流資料的案例，我們只能將篩選套用到目前和較舊的點上。 不過，您可以將它設定為 `true` ，讓它與時間序列保持同步，以便進行特定處理。 請參閱下列範例。 此參數會控制篩選的 [群組延遲](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)。

## <a name="examples"></a>範例

* 藉由設定*filter*= [1，1，1，1，1] 來計算五個點的移動*平均，並*正規化 = `true` (預設) 。 請注意*center* = `false` (預設) 的效果與 `true` ：

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
*5h_MovingAvg*：移動平均篩選的五個點。 會將響應平滑化，且其高峰會移位 (5-1)/2 = 2 小時。  
*5h_MovingAvg_centered*：相同但藉由設定 `center=true` ，尖峰會維持在其原始位置。

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="數列杉樹" border="false":::

* 若要計算某個點與其前一個點之間的差異，請設定 *filter*= [1，-1]。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="數列杉樹" border="false":::
