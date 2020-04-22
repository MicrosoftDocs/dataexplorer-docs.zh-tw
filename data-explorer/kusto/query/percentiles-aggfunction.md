---
title: 百分位數(),百分位數() - Azure 數據資源管理器 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的百分位數())。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: ecbb56305cfc43033ca172071f48b25768de6d2f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663651"
---
# <a name="percentile-percentiles"></a>百分位數(),百分位數()

返回*Expr*定義的填充的指定[最近排名百分位數的](#nearest-rank-percentile)估計值。 其精確度取決於百分位數區域中的母體密度。 此功能只能在[匯總](summarizeoperator.md)中的集合上下文中使用

* `percentiles()`與`percentile()`類似,但計算多個百分位值(這比單獨計算每個百分位數更快)。
* `percentilesw()`與`percentilew()`類似,但計算一些加權百分位數值(這比單獨計算每個百分位數更快)。
* `percentilew()`並允許`percentilesw()`計算加權百分位數。 加權百分位數以「加權」方式計算給定百分位數 - 將每個值視為輸入`Weight`中重複 的時間。

**語法**

總結`percentile(` *Expr* `,` *百分位數*`)`

匯總`percentiles(` *Expr* `,` *百分位數1* =`,` *百分位數 2*|`)`

匯總`percentiles_array(` *Expr* `,` *百分位數1* =`,` *百分位數 2*|`)`

匯總`percentiles_array(` *Expr* `,` *動態陣列*`)`

總結`percentilew(` *Expr* `,` *重量 Expr* `,` *百分位數*`)`

總結`percentilesw(` *Expr* `,`*重量 Expr* `,` `,`*百分位數1* =*百分位數2*|`)`

總結`percentilesw_array(` *Expr* `,`*重量 Expr* `,` `,`*百分位數1* =*百分位數2*|`)`

匯總`percentilesw_array(` *Expr* `,` *重量 Expr* `,` *動態陣列*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。
* *權重Expr*:運算式,用作聚合計算值的權重。
* *百分位數*是指定百分位數的雙常量。
* *動態陣列*:整數或浮點數的動態陣列中的百分位數清單。

**傳回**

返回組中指定百分位數的*Expr*估計值。 

**範例**

的值`Duration`大於樣本集的 95%和小於樣本集的 5%:

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

同時計算 5、50(中位數)和 95:

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/aggregations/percentiles.png" alt-text="百分位數":::

結果顯示,在歐洲,5%的呼叫短於11.55秒,50%的呼叫短於3分鐘,18.46秒,95%的呼叫短於40分48秒。

計算多個統計資料︰

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>加權的百分位數

假設您測量了一個操作需要一次又一次完成的時間(持續時間)。 而不是記錄測量的每個值,您通過記錄每個值「持續時間」(四捨五入到 100 毫秒)以及圓舍入值出現的次數(BucketSize)來壓縮它們。

您可以使用以`summarize percentilesw(Duration, BucketSize, ...)`「加權」方式計算給定百分位數 - 將持續時間的每個值視為在輸入中重複的 BucketSize 時間,而無需實際實現這些記錄。

範例:客戶具有一組以毫秒為單位的延遲值: `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`。

要減少頻寬和儲存,請對以下儲存桶執行預聚合:`{ 10, 20, 30, 40, 50, 100 }`並計算每個儲存桶中的事件數,這給出了以下 Kusto 表:

:::image type="content" source="images/aggregations/percentilesw-table.png" alt-text="百分位數表":::

可以讀取為:
 * 10ms 儲存桶中的 8`{ 1, 1, 2, 2, 2, 5, 7, 7 }`個事件 (對應於子集)
 * 20ms 儲存桶中的 6`{ 12, 12, 15, 15, 15, 18 }`個事件 (對應於子集)
 * 30ms 儲存桶中的 3`{ 21, 22, 26 }`個事件 (對應於子集)
 * 40ms 儲存桶中的 1`{ 35 }`個事件 (對應於子集)

此時,原始數據不再可用,我們擁有的所有數據就是每個存儲桶中的事件數。 要計算此資料的百分位數,請使用函數`percentilesw()`。 例如,對於 50、75 和 99.9 百分位數,請使用以下查詢: 

```kusto
datatable (ReqCount:long, LatencyBucket:long) 
[ 
    8, 10, 
    6, 20, 
    3, 30, 
    1, 40 
]
| summarize percentilesw(LatencyBucket, ReqCount, 50, 75, 99.9) 
```

上述查詢的結果如下:

:::image type="content" source="images/aggregations/percentilesw-result.PNG" alt-text="百分位數sw結果":::

請注意,如果資料消耗到以下表單,`percentiles(LatencyBucket, 50, 75, 99.9)`則上述查詢對應於函數:

:::image type="content" source="images/aggregations/percentilesw-rawtable.png" alt-text="百分位數sw 原始表":::

## <a name="getting-multiple-percentiles-in-an-array"></a>在陣列中取得多個百分位數

可以在單個動態列中作為陣列獲得多個百分位數,而不是多列: 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/aggregations/percentiles-array-result.png" alt-text="百分位數陣列結果":::

同樣,加權百分位數也可以作為動態陣列返回,`percentilesw_array`

的`percentiles_array`百分`percentilesw_array`位數 可以在整數或浮點數的動態數位中指定。 陣列必須是常量陣列,但不一定是文本陣列。

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>最近等級百分位數
*排序*值清單的 P-th 百分位數 (0 < *P* <= 100) 是清單中最小的值,因此*P%* 的資料小於或等於該值([摘自維基百科文章關於百分位數](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method))

將*0*- 第 1 個百分位數定義為總體中最小的成員。

>[!NOTE]
> 給定計算的近似性質,實際返回的值可能不是總體的成員。
> 最近等級定義表示*P*=50 不符合[中位數的插值定義](https://en.wikipedia.org/wiki/Median)。 在評估這種差異對特定應用的重要性時,應考慮總體大小和[估計誤差](#estimation-error-in-percentiles)。

## <a name="estimation-error-in-percentiles"></a>百分位數中的估計誤差

百分位數彙總使用 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)提供近似值。 

>[!NOTE]
> * 估計誤差的範圍會隨著要求的百分位數值而變化。 最佳精度位於 [0..100] 刻度的末端。 百分位數 0 和 100 是分佈的精確最小值和最大值。 精確度會逐漸向級別中央減少。 在中位數中,這是最差的,上限為1%。 
> * 誤差範圍是在秩上測得，而非在值上。 假設百分位數(X,50)返回一個值 Xm。 該估計保證至少 49% 和最多 51% 的 X 值小於或等於 Xm。 Xm 和 X 的實際中值之間的差異沒有理論限制。
> * 估計有時可能導致精確的值,但沒有可靠的條件來定義何時將情況。
