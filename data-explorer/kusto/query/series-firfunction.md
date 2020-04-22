---
title: series_fir() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_fir()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: bbdf93504be431d77c19a881cf79d1e1d3d400ac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663580"
---
# <a name="series_fir"></a>series_fir()

在序列上應用有限脈衝回應篩選器。  

以包含動態數值陣列的運算式作為輸入,並應用[有限脈衝回應](https://en.wikipedia.org/wiki/Finite_impulse_response)篩選器。 通過指定`filter`係數,可用於計算移動平均線、平滑、變化檢測和更多用例。 此函式會採用包含濾波器係數的動態陣列和靜態動態陣列之資料行作為輸入，並且會套用資料行上的篩選條件。 它會輸出新的動態陣列資料行，其中包含已篩選的輸出。  

**語法**

`series_fir(`*x*`,`x*filter*篩選`,`器`,`=*規範化*[*中心*]`)`

**引數**

* *x*: 動態陣列儲存格,它是數值陣列,通常是[生成序列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算符的輸出。
* *篩選器*:包含篩選器係數的常量運算式(儲存為數值的動態陣列)。
* *規範化*:可選的布爾值,指示是否應對篩選器進行規範化(即除以係數之和)。 如果*過濾器*包含負值 *,則規範化*必須指定為`false``null`,否則結果將為 。 如果未指定,則將根據*篩選器*中存在負值,假定*規範化*的預設值:如果*篩選器*至少包含一個負值,`false`則*假定規範化*為 。  
規範化是確保係數之和為 1 的便捷方法,因此濾波器不會放大或衰減序列。 例如,4 個條柱的移動平均線可以通過*篩選器*[1,1,1,1] 和*規範化*_true 指定,這比鍵入 [0.25,0.25.25,0.25]更容易。
* *中心*:一個可選的布爾值,指示篩選器是在當前點之前和之後對稱地應用於時間視窗,還是從當前點向後的時間視窗上應用。 依預設，置中為 false，適用於串流資料的案例，其中我們只可將濾波器套用在目前點和較舊的點；不過，針對臨機操作處理，您可將它設為 true，讓它與時間序列保持同步 (請參閱以下範例)。 從技術上講,此參數控制濾波器的[組延遲](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)。

**範例**

* 計算移動平均線 5 點可以通過設置*篩選器*[1,1,1,1,1] 並*規範化*=`true`(預設值)來執行。 請注意*中心*=`false`(預設)`true`與的影響 :

```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

此查詢會傳回︰  
*5h_MovingAvg*: 5 點移動平均濾波器。 會將響應平滑化，且其高峰會移位 (5-1)/2 = 2 小時。  
*5h_MovingAvg_centered*: 相同,但與設定中心_true, 使峰值停留在其原始位置.

:::image type="content" source="images/samples/series-fir.png" alt-text="系列冷杉系列":::

* 可以通過設置*篩選器*[1,-1] 來計算點與其前面的點之間的差異:

```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```
:::image type="content" source="images/samples/series-fir2.png" alt-text="系列冷杉 2":::
