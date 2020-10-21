---
title: '百分位數 ( # A1、百分位數 ( # A3-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1、百分位數 ( # A3 的百分位數。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 6b48a962e1016b681525c9ee493a7a8a6e436395
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249718"
---
# <a name="percentile-percentiles-aggregation-function"></a>百分位數 ( # A1、百分位數 ( # A3 (彙總函式) 

傳回所定義之擴展的指定 [最接近排名百分](#nearest-rank-percentile) 位數的估計值 `*Expr*` 。
其精確度取決於百分位數區域中的母體密度。 這個函式只能用在[摘要內匯總](summarizeoperator.md)的內容中

* `percentiles()` 很類似 `percentile()` ，但會計算百分位數值，比個別計算每個百分位數更快。
* `percentilesw()` 很類似 `percentilew()` ，但會計算一些加權百分位數值，比個別計算每個百分位數更快。
* `percentilew()` 並 `percentilesw()` 讓您計算加權百分位數。 加權百分位數會以「加權」方式計算指定的百分位數，方法是在輸入中將每個值視為重複的 `weight` 時間。

## <a name="syntax"></a>語法

摘要 `percentile(` *Expr* `,` *百分位數*`)`

摘要 `percentiles(` *運算式* `,` *Percentile1* [ `,` *Percentile2*]`)`

摘要 `percentiles_array(` *運算式* `,` *Percentile1* [ `,` *Percentile2*]`)`

摘要 `percentiles_array(` *運算式* `,` *動態陣列*`)`

摘要 `percentilew(` *運算式* `,` *WeightExpr* `,` *百分位數*`)`

摘要 `percentilesw(` *運算式* `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

摘要 `percentilesw_array(` *運算式* `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

摘要 `percentilesw_array(` *運算式* `,` *WeightExpr* `,` *動態陣列*`)`

## <a name="arguments"></a>引數

* `*Expr*`：將用於匯總計算的運算式。
* `*WeightExpr*`：將用來當做匯總計算值加權的運算式。
* `*Percentile*`：指定百分位數的雙常數。
* `*Dynamic array*`：整數或浮點數的動態陣列中的百分位數清單。

## <a name="returns"></a>傳回

傳回 `*Expr*` 群組中指定之百分位數的估計值。 

## <a name="examples"></a>範例

的值大於 `Duration` 取樣集的95%，且小於樣本集的5%。

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

同時計算5、50 (中位數) 和95。

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/percentiles-aggfunction/percentiles.png" alt-text="列出結果的表格，其中包含大陸的資料行和第五、fiftieth 和九十-第五百分位數的 duration 值。":::

結果顯示在歐洲，5% 的呼叫小於 11.55 s，50% 的呼叫小於3分鐘、18.46 秒，而95% 的呼叫小於40分鐘的48秒。

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>加權的百分位數

假設您重複測量時間 (持續時間) 它會採取動作來完成。 您可以記錄每個持續時間值（四捨五入至100毫秒），以及舍入值 (BucketSize) 的次數，而不是錄製每個量值。

使用 `summarize percentilesw(Duration, BucketSize, ...)` 以「加權」方式計算指定的百分位數。 將持續時間的每個值視為重複輸入中的 BucketSize 時間，而不需要具體化這些記錄。

## <a name="example"></a>範例

客戶有一組延遲值（以毫秒為單位）： `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }` 。

若要減少頻寬和儲存空間，請將下列值區預先匯總： `{ 10, 20, 30, 40, 50, 100 }` 。 計算每個值區中的事件數目，以產生下表：

:::image type="content" source="images/percentiles-aggfunction/percentilesw-table.png" alt-text="列出結果的表格，其中包含大陸的資料行和第五、fiftieth 和九十-第五百分位數的 duration 值。":::

資料表會顯示：
 * 10毫秒值區中的八個事件 (對應至子集 `{ 1, 1, 2, 2, 2, 5, 7, 7 }`) 
 * 20 (毫秒值區中有六個事件對應至子集 `{ 12, 12, 15, 15, 15, 18 }`) 
 * 30-ms 值區中的三個事件 (對應至子集 `{ 21, 22, 26 }`) 
 * 40-ms 值區中的一個事件 (對應至子集 `{ 35 }`) 

此時將無法再使用原始資料。 只有每個值區中的事件數目。 若要計算此資料的百分位數，請使用 `percentilesw()` 函數。
例如，針對50、75和99.9 百分位數，請使用下列查詢。

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

:::image type="content" source="images/percentiles-aggfunction/percentilesw-result.png" alt-text="列出結果的表格，其中包含大陸的資料行和第五、fiftieth 和九十-第五百分位數的 duration 值。" border="false":::


`percentiles(LatencyBucket, 50, 75, 99.9)`如果資料是以下列形式展開，則上述查詢會對應至函數：

:::image type="content" source="images/percentiles-aggfunction/percentilesw-rawtable.png" alt-text="列出結果的表格，其中包含大陸的資料行和第五、fiftieth 和九十-第五百分位數的 duration 值。":::

## <a name="getting-multiple-percentiles-in-an-array"></a>取得陣列中的多個百分位數

您可以在單一動態資料行中以陣列形式取得多個百分位數，而不是在多個資料行中取得。

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/percentiles-aggfunction/percentiles-array-result.png" alt-text="列出結果的表格，其中包含大陸的資料行和第五、fiftieth 和九十-第五百分位數的 duration 值。":::

同樣地，加權百分位數可以使用動態陣列傳回 `percentilesw_array` 。

您 `percentiles_array` `percentilesw_array` 可以在整數或浮點數的動態陣列中指定和的百分位數。 陣列必須是常數，但不一定要是常值。

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>最接近排名百分位數

*第 P*個百分位數 (0 < *P* <= 100) 排序值清單（從最小到最大排序）是清單中最小的值。 [從百分位數) 的維琪百科文章](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method) (的資料的*p* % 小於或等於*p*個百分位數值。

將第 *0*個百分位數定義為人口的最小成員。

>[!NOTE]
> 由於計算的將逼近本質，實際傳回的值可能不是人口的成員。
> 最接近排名的定義表示 *P*= 50 不符合中間值的 [interpolative 定義](https://en.wikipedia.org/wiki/Median)。 針對特定應用程式評估這項差異的重要性時，應考慮人口和 [估計錯誤](#estimation-error-in-percentiles) 的大小。

## <a name="estimation-error-in-percentiles"></a>百分位數中的估計誤差

百分位數彙總使用 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)提供近似值。

>[!NOTE]
> * 估計誤差的範圍會隨著要求的百分位數值而變化。 最佳的精確度是在 [0.. 100] 小數位數的兩端。 百分位數0和100是分佈的確切最小值和最大值。 精確度會逐漸向級別中央減少。 它在中間值是最差的，且上限為1%。
> * 誤差範圍是在秩上測得，而非在值上。 假設百分位數 (X，50) 傳回的值為 Xm。 預估可保證 X 的最高49% 和最多51% 的值小於或等於 Xm。 在 [Xm] 和 [實際的中間值 X] 之間沒有任何理論上的限制。
> * 預估有時可能會產生精確的值，但沒有可靠的條件可定義何時會發生這種情況。
