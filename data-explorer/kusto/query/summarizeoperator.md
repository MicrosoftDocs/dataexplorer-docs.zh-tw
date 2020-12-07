---
title: summarize 運算子 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 summarize 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.localizationpriority: high
ms.openlocfilehash: 810eba264c717d156f74b9958edecb712d58a4fd
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513194"
---
# <a name="summarize-operator"></a>summarize 運算子

產生資料表來彙總輸入資料表的內容。

```kusto
Sales | summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime)
```

傳回的資料表包含銷售交易數量及每樣水果和每個銷售月份的總金額。
輸出資料行會顯示交易計數、交易價值、水果，以及記錄交易的月份開始日期時間。

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

顯示有多少項目的價格落在 [0,10.0]、[10.0,20.0] 等依此類推的間隔中的資料表。 此範例有一個用於放置計數的資料行，以及一個用於放置價格範圍的資料行。 其他所有輸入資料行則會遭到忽略。

## <a name="syntax"></a>語法

*T* `| summarize` [[*Column* `=`] *Aggregation* [`,` ...]] [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>引數

*  結果資料行的選擇性名稱。 預設值為衍生自運算式的名稱。
* [彙總：](summarizeoperator.md#list-of-aggregation-functions)對 `count()` 或 `avg()` 等[彙總函式](summarizeoperator.md#list-of-aggregation-functions)進行的呼叫，以資料行名稱作為引數。 請參閱 [彙總函式清單](summarizeoperator.md#list-of-aggregation-functions)。
* *GroupExpression：* 可以參考輸入資料的純量運算式。
  輸出中的記錄會與所有群組運算式的相異值一樣多。

> [!NOTE]
> 當輸入資料表是空的時，輸出取決於是否使用 GroupExpression：
>
> * 如果未提供 GroupExpression，輸出將會是單一 (空白) 資料列。
> * 如果提供 GroupExpression，輸出就不會有任何資料列。

## <a name="returns"></a>傳回

輸入資料列會各自分組到具有相同 `by` 運算式值的群組。 然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。 結果會包含 `by` 資料行，而且每個經過計算的彙總至少會佔有一個資料行。 (某些彙總函式會傳回多個資料行)。

`by` 值 (可以是零) 有多少個不同組合，結果就會有多少個資料列。 如果未提供任何群組索引鍵，則結果會有一筆記錄。

若要要彙總數值範圍，請使用 `bin()` 來將範圍減少為離散值。

> [!NOTE]
> * 雖然您可以為彙總與群組運算式提供任意運算式，但更有效率的方法是使用簡單的資料行名稱，或對數值資料行套用 `bin()` 。
> * 日期時間資料行的自動每小時間隔功能已不再受到支援。 請改用明確的間隔。 例如： `summarize by bin(timestamp, 1h)` 。

## <a name="list-of-aggregation-functions"></a>彙總函式清單

|函式|描述|
|--------|-----------|
|[any()](any-aggfunction.md)|傳回群組的隨機非空白值|
|[anyif()](anyif-aggfunction.md)|傳回群組的隨機非空白值 (含述詞)|
|[arg_max()](arg-max-aggfunction.md)|當引數最大化時，傳回一個或多個運算式|
|[arg_min()](arg-min-aggfunction.md)|當引數最小化時，傳回一個或多個運算式|
|[avg()](avg-aggfunction.md)|傳回整個群組的平均值|
|[avgif()](avgif-aggfunction.md)|傳回整個群組的平均值 (含述詞)|
|[binary_all_and](binary-all-and-aggfunction.md)|使用群組的二進位 `AND` 傳回彙總值|
|[binary_all_or](binary-all-or-aggfunction.md)|使用群組的二進位 `OR` 傳回彙總值|
|[binary_all_xor](binary-all-xor-aggfunction.md)|使用群組的二進位 `XOR` 傳回彙總值|
|[buildschema()](buildschema-aggfunction.md)|傳回容許 `dynamic` 輸入所有值的最小結構描述|
|[count()](count-aggfunction.md)|傳回群組的計數|
|[countif()](countif-aggfunction.md)|傳回包含群組述詞的計數|
|[dcount()](dcount-aggfunction.md)|傳回群組元素的近似相異計數|
|[dcountif()](dcountif-aggfunction.md)|傳回群組元素的近似相異計數 (含述詞)|
|[make_bag()](make-bag-aggfunction.md)|傳回群組內動態值的屬性包。|
|[make_bag_if()](make-bag-if-aggfunction.md)|傳回群組內動態值的屬性包 (含述詞)|
|[make_list()](makelist-aggfunction.md)|傳回群組內所有值的清單|
|[make_list_if()](makelistif-aggfunction.md)|傳回群組內所有值的清單 (含述詞)|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|傳回群組中所有值的清單，包括 Null 值|
|[make_set()](makeset-aggfunction.md)|傳回群組內的一組相異值|
|[make_set_if()](makesetif-aggfunction.md)|傳回群組內的一組相異值 (含述詞)|
|[max()](max-aggfunction.md)|傳回整個群組的最大值|
|[maxif()](maxif-aggfunction.md)|傳回整個群組的最大值 (含述詞)|
|[min()](min-aggfunction.md)|傳回整個群組的最小值|
|[minif()](minif-aggfunction.md)|傳回整個群組的最小值 (含述詞)|
|[percentiles()](percentiles-aggfunction.md)|傳回群組的百分位數近似值|
|[percentiles_array()](percentiles-aggfunction.md)|傳回群組的百分位數近似值|
|[percentilesw()](percentiles-aggfunction.md)|傳回群組的加權百分位近似值|
|[percentilesw_array()](percentiles-aggfunction.md)|傳回群組的加權百分位數近似值|
|[stdev()](stdev-aggfunction.md)|傳回整個群組的標準差|
|[stdevif()](stdevif-aggfunction.md)|傳回整個群組的標準差 (含述詞)|
|[sum()](sum-aggfunction.md)|傳回群組內元素的總和|
|[sumif()](sumif-aggfunction.md)|傳回群組內元素的總和 (含述詞)|
|[variance()](variance-aggfunction.md)|傳回整個群組的變異數|
|[varianceif()](varianceif-aggfunction.md)|傳回整個群組的變異數 (含述詞)|

## <a name="aggregates-default-values"></a>彙總預設值

下表是彙總預設值的摘要：

運算子       |預設值                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    空的動態陣列              ([])          
 All others          |   null                           

 在包含 Null 值的實體上使用這些彙總時，將會忽略 Null 值，而且不會參與計算 (請參閱下列範例)。

## <a name="examples"></a>範例

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="依水果與供應商的價格總結":::

## <a name="example-unique-combination"></a>範例：唯一組合

判斷資料表中唯一的 `ActivityType` 和 `CompletionStatus` 組合為何。 沒有彙總函式，只有 group-by 索引鍵。 輸出只會顯示這些結果的資料行：

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example-minimum-and-maximum-timestamp"></a>範例：最小和最大時間戳記

尋找「活動」資料表中所有記錄的最小和最大時間戳記。 由於沒有 group-by 子句，因此輸出中只有一個資料列︰

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example-distinct-count"></a>範例：相異計數

為每個洲建立一個資料列，以顯示發生活動的城市計數。 由於「洲」的值不多，因此 'by' 子句中不需要有群組函式：

```kusto
Activities | summarize cities=dcount(city) by continent
```

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example-histogram"></a>範例：長條圖

下列範例會計算每個活動類型的長條圖。 因為 `Duration` 有許多值，請使用 `bin` 將其值分組成 10 分鐘的間隔：

```kusto
Activities | summarize count() by ActivityType, length=bin(Duration, 10m)
```

|`count_`|`ActivityType`|`length`
|---:|---|---
|`354`| `dancing` | `0:00:00.000`
|`23`|`singing` | `0:00:00.000`
|`2717`|`dancing`|`0:10:00.000`
|`341`|`singing`|`0:10:00.000`
|`725`|`dancing`|`0:20:00.000`
|`2876`|`singing`|`0:20:00.000`
|...

**彙總預設值的範例**

如果 `summarize` 運算子的輸入至少有一個空白的 group-by 索引鍵，其結果也會是空的。

如果 `summarize` 運算子的輸入沒有空白的 group-by 索引鍵，則結果會是 `summarize` 中使用的彙總預設值：

```kusto
datatable(x:long)[]
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
datatable(x:long)[]
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
datatable(x:long)[]
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

avg 彙總會總結所有非 Null 的值，而且只會計算參與計算的值 (不會將 Null 列入考慮)。

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

一般計數會計算 Null： 

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize count(y)
```

|count_y|
|---|
|2|

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize make_set(y), make_set(y)
```

|set_y|set_y1|
|---|---|
|[5.0]|[5.0]|
