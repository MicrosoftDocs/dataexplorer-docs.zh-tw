---
title: 百分位數（）、百分位數（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的百分位數（）、百分位數（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 58d6458f0a5cf514b1acd240c9adede2022f028b
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619078"
---
# <a name="percentile-percentiles"></a>百分位數（）、百分位數（）

針對*Expr*所定義之擴展的指定[最近排名百分](#nearest-rank-percentile)位數，傳回估計值。 其精確度取決於百分位數區域中的母體密度。 此函式只能用在[摘要內匯總](summarizeoperator.md)的內容中

* `percentiles()`類似于`percentile()`，但會計算多個百分位數值（速度會比個別計算每個百分位數快）。
* `percentilesw()`類似于`percentilew()`，但會計算多個加權百分位數值（速度會比個別計算每個百分位數更快）。
* `percentilew()`和`percentilesw()`可計算加權百分位數。 加權百分位數會以「加權」方式計算指定的百分位數，將每個值視為輸入中重複`Weight`的時間。

**語法**

匯總`percentile(` *Expr* `,` *百分位數*`)`

總結`percentiles(` *Expr* `,` *Percentile1* [`,` *Percentile2*]`)`

總結`percentiles_array(` *Expr* `,` *Percentile1* [`,` *Percentile2*]`)`

摘要`percentiles_array(` *Expr* `,` *動態陣列*`)`

總結`percentilew(` *Expr* `,` *WeightExpr* WeightExpr`,`個*百分位數*`)`

總結`percentilesw(` *Expr* `,` *WeightExpr* WeightExpr`,` *Percentile1* [`,` *Percentile2*]`)`

總結`percentilesw_array(` *Expr* `,` *WeightExpr* WeightExpr`,` *Percentile1* [`,` *Percentile2*]`)`

摘要`percentilesw_array(` *Expr* `,` *WeightExpr* WeightExpr`,` *動態陣列*`)`

**引數**

* *Expr*：將用於匯總計算的運算式。
* *WeightExpr*：將當做匯總計算之值的權數使用的運算式。
* *百分*位數是指定百分位數的 double 常數。
* *動態陣列*：整數或浮點數的動態陣列中的百分位數清單。

**傳回**

傳回群組中指定之百分位數的*Expr*估計值。 

**範例**

大於 95% `Duration`樣本集和小於5% 樣本集的值：

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

同時計算5、50（中位數）和95：

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/percentiles-aggfunction/percentiles.png" alt-text="百分位數":::

結果顯示在歐洲，5% 的呼叫少於 11.55 s，50% 的呼叫少於3分鐘、18.46 秒，而95% 的呼叫少於40分鐘的48秒。

計算多個統計資料︰

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>加權的百分位數

假設您測量時間（持續時間），它會再次採取動作以再次完成。 您不需要錄製測量的每個值，而是將每個持續時間值（四捨五入為100毫秒）和舍入值出現多少次（BucketSize），以將其壓縮。

您可以使用`summarize percentilesw(Duration, BucketSize, ...)`來以「加權」方式計算給定的百分位數，將每個持續時間值視為在輸入中重複 BucketSize 時間，而不需要實際地具體化那些記錄。

範例：客戶具有一組延遲值（以毫秒為單位`{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`）：。

若要減少頻寬和儲存空間，請對下列值區執行預先`{ 10, 20, 30, 40, 50, 100 }`匯總：，並計算每個值區中的事件數目，這會提供下列 Kusto 資料表：

:::image type="content" source="images/percentiles-aggfunction/percentilesw-table.png" alt-text="Percentilesw 資料表":::

可以讀取為：
 * 10毫秒值區中的8個事件（對應`{ 1, 1, 2, 2, 2, 5, 7, 7 }`至子集）
 * 20毫秒值區中的6個事件（對應`{ 12, 12, 15, 15, 15, 18 }`至子集）
 * 30毫秒值區中的3個事件（對應`{ 21, 22, 26 }`至子集）
 * 40毫秒值區中的1個事件（對應`{ 35 }`至子集）

此時，原始資料已無法再使用，而我們只是每個 bucket 中的事件數目。 若要計算此資料的百分位數，請`percentilesw()`使用函數。 例如，針對50、75和99.9 百分位數，請使用下列查詢： 

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

上述查詢的結果為：


:::image type="content" source="images/percentiles-aggfunction/percentilesw-result.png" alt-text="Percentilesw 結果" border="false":::


請注意，如果資料是由下列形式`percentiles(LatencyBucket, 50, 75, 99.9)`所組成，則上述查詢會對應至函數：

:::image type="content" source="images/percentiles-aggfunction/percentilesw-rawtable.png" alt-text="Percentilesw 原始資料表":::

## <a name="getting-multiple-percentiles-in-an-array"></a>取得陣列中的多個百分位數

在單一動態資料行中，可以將多個百分位數當做陣列來取得，而不是多個資料行： 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/percentiles-aggfunction/percentiles-array-result.png" alt-text="百分位數陣列結果":::

同樣地，您也可以使用，以動態陣列的形式傳回加權百分位數`percentilesw_array`

和`percentilesw_array`的`percentiles_array`百分位數可以在整數或浮點數的動態陣列中指定。 陣列必須是常數，但不一定要是常值。

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>最接近排名百分位數
已排序值清單的*第 P*個百分位數（0 < *P* <= 100）是清單中的最小值，因此*P* % 的資料小於或等於該值（[從百分位數上的維琪百科文章](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method)）

將第*0*個百分位數定義為擴展的最小成員。

>[!NOTE]
> 假設計算的將逼近性質，實際傳回的值可能不是擴展的成員。
> 最接近的排名定義表示*P*= 50 不符合中間值的[interpolative 定義](https://en.wikipedia.org/wiki/Median)。 針對特定應用程式評估這項差異的重要性時，應該將擴展的大小和[估計錯誤](#estimation-error-in-percentiles)納入考慮。

## <a name="estimation-error-in-percentiles"></a>百分位數中的估計誤差

百分位數彙總使用 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)提供近似值。 

>[!NOTE]
> * 估計誤差的範圍會隨著要求的百分位數值而變化。 最佳精確度位於 [0-100] 尺規的結尾。 百分位數0和100是散發的確切最小和最大值。 精確度會逐漸向級別中央減少。 它在中間值最差，且上限為1%。 
> * 誤差範圍是在秩上測得，而非在值上。 假設百分位數（X，50）傳回的值為 Xm。 估計可保證 X 的值至少49% 和最多51% 的值小於或等於 Xm。 在 Xm 與 X 的實際中間值之間，沒有任何理論上的限制。
> * 估計有時可能會產生精確的值，但沒有可靠的條件可定義何時會發生這種情況。
