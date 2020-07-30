---
title: 摘要運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的摘要運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.openlocfilehash: a200d0619b25fe7410a82a941a3b1bf6e35d60ac
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342609"
---
# <a name="summarize-operator"></a>summarize 運算子

產生資料表來彙總輸入資料表的內容。

```kusto
T | summarize count(), avg(price) by fruit, supplier
```

顯示每個供應商之每個水果的數目和平均價格的資料表。 針對水果和供應商的每個相異組合，輸出中會有一個資料列。 輸出資料行會顯示 [計數]、[平均價格]、[水果] 和 [供應商]。 其他所有輸入資料行則會遭到忽略。

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

顯示有多少項目的價格落在 [0,10.0]、[10.0,20.0] 等依此類推的間隔中的資料表。 此範例有一個用於放置計數的資料行，以及一個用於放置價格範圍的資料行。 其他所有輸入資料行則會遭到忽略。

## <a name="syntax"></a>語法

*T* `| summarize` [[*column* `=` ]*匯總*[ `,` ...]] [ `by` [*column* `=` ] *GroupExpression* [ `,` ...]]

## <a name="arguments"></a>引數

* ** 結果資料行的選擇性名稱。 預設值為衍生自運算式的名稱。
* *匯總：* 以資料[aggregation function](summarizeoperator.md#list-of-aggregation-functions) `count()` `avg()` 行名稱做為引數呼叫彙總函式（例如或）。 請參閱 [彙總函式清單](summarizeoperator.md#list-of-aggregation-functions)。
* ** 可提供一組相異值的資料行運算式。 它通常是已提供一組受限值的資料行名稱，或是以數值或時間資料行做為引數的 `bin()` 。 

> [!NOTE]
> 當輸入資料表是空的時，輸出取決於是否使用*GroupExpression* ：
>
> * 如果未提供*GroupExpression* ，輸出將會是單一（空白）資料列。
> * 如果提供*GroupExpression* ，輸出就不會有任何資料列。

## <a name="returns"></a>傳回

輸入資料列會各自分組到具有相同 `by` 運算式值的群組。 然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。 結果會包含 `by` 資料行，而且每個經過計算的彙總至少會佔有一個資料行。 (某些彙總函式會傳回多個資料行)。

結果有多個不同的 `by` 值組合（可能是零）的資料列。 如果未提供任何群組索引鍵，則結果會有一筆記錄。

若要總結數值範圍，請使用 `bin()` 來將範圍減少為離散值。

> [!NOTE]
> * 雖然您可以為彙總與群組運算式提供任意運算式，但更有效率的方法是使用簡單的資料行名稱，或對數值資料行套用 `bin()` 。
> * 不再支援 datetime 資料行的自動每小時。 請改用明確的分類收納。 例如： `summarize by bin(timestamp, 1h)` 。

## <a name="list-of-aggregation-functions"></a>彙總函式的清單

|函式|描述|
|--------|-----------|
|[any （）](any-aggfunction.md)|傳回群組的隨機非空白值|
|[anyif()](anyif-aggfunction.md)|傳回群組的隨機非空白值（含述詞）|
|[arg_max()](arg-max-aggfunction.md)|當引數最大化時，傳回一或多個運算式|
|[arg_min()](arg-min-aggfunction.md)|當引數最小化時，傳回一或多個運算式|
|[avg （）](avg-aggfunction.md)|傳回整個群組的平均值|
|[avgif()](avgif-aggfunction.md)|傳回整個群組的平均值（含述詞）|
|[binary_all_and](binary-all-and-aggfunction.md)|使用群組的二進位檔傳回匯總值 `AND`|
|[binary_all_or](binary-all-or-aggfunction.md)|使用群組的二進位檔傳回匯總值 `OR`|
|[binary_all_xor](binary-all-xor-aggfunction.md)|使用群組的二進位檔傳回匯總值 `XOR`|
|[buildschema()](buildschema-aggfunction.md)|傳回 dynamicexpression 輸入之所有值的最小架構 `dynamic`|
|[count （）](count-aggfunction.md)|傳回群組的計數|
|[countif()](countif-aggfunction.md)|傳回具有群組之述詞的計數|
|[dcount()](dcount-aggfunction.md)|傳回群組元素的近似相異計數|
|[dcountif()](dcountif-aggfunction.md)|傳回群組元素的近似相異計數（含述詞）|
|[make_bag()](make-bag-aggfunction.md)|傳回群組內動態值的屬性包|
|[make_bag_if()](make-bag-if-aggfunction.md)|傳回群組內動態值的屬性包（使用述詞）|
|[make_list()](makelist-aggfunction.md)|傳回群組內所有值的清單|
|[make_list_if()](makelistif-aggfunction.md)|傳回群組內所有值的清單（含述詞）|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|傳回群組中所有值的清單，包括 null 值|
|[make_set()](makeset-aggfunction.md)|傳回群組內的一組相異值|
|[make_set_if()](makesetif-aggfunction.md)|傳回群組內的一組相異值（使用述詞）|
|[max()](max-aggfunction.md)|傳回整個群組的最大值|
|[maxif()](maxif-aggfunction.md)|傳回整個群組的最大值（含述詞）|
|[min()](min-aggfunction.md)|傳回整個群組的最小值|
|[minif()](minif-aggfunction.md)|傳回整個群組的最小值（含述詞）|
|[百分位數（）](percentiles-aggfunction.md)|傳回群組的百分位數近似值|
|[percentiles_array （）](percentiles-aggfunction.md)|傳回群組的百分位數近似|
|[percentilesw()](percentiles-aggfunction.md)|傳回群組的加權百分位近似|
|[percentilesw_array （）](percentiles-aggfunction.md)|傳回群組的加權百分位數近似|
|[stdev （）](stdev-aggfunction.md)|傳回整個群組的標準差|
|[stdevif()](stdevif-aggfunction.md)|傳回整個群組的標準差（使用述詞）|
|[sum （）](sum-aggfunction.md)|傳回遷移群組的元素總和|
|[sumif()](sumif-aggfunction.md)|傳回遷移群組之元素的總和（使用述詞）|
|[variance()](variance-aggfunction.md)|傳回整個群組的變異數|
|[varianceif()](varianceif-aggfunction.md)|傳回整個群組的變異數（使用述詞）|

## <a name="aggregates-default-values"></a>匯總預設值

下表摘要說明匯總的預設值：

運算子       |預設值                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    空的動態陣列（[]）          
 All others          |   null                           

 在包含 null 值的實體上使用這些匯總時，將會忽略 null 值，而且不會參與計算（請參閱下列範例）。

## <a name="examples"></a>範例

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="依水果與供應商的價格總結":::

## <a name="example"></a>範例

判斷 `ActivityType` 和資料表中的唯一組合 `CompletionStatus` 。 沒有任何彙總函式，只是群組依據索引鍵。 輸出只會顯示這些結果的資料行：

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example"></a>範例

尋找 [活動] 資料表中所有記錄的最小和最大時間戳記。 由於沒有 group-by 子句，因此輸出中只有一個資料列︰

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example"></a>範例

為每個大陸建立一個資料列，其中顯示活動發生的城市計數。 因為「大陸」有幾個值，' by ' 子句中不需要有群組函數：

    Activities | summarize cities=dcount(city) by continent

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example"></a>範例

下列範例會計算每個活動類型的長條圖。 因為 `Duration` 有許多值，請使用 `bin` 將其值分組成10分鐘的間隔：

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

**匯總預設值的範例**

當運算子的輸入 `summarize` 至少有一個空白的 group by 索引鍵時，也會是空的結果。

當運算子的輸入沒有 `summarize` 空白的 group by 索引鍵時，結果會是中使用之匯總的預設值 `summarize` ：

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

匯總所有非 null 的平均值，而且只會計算參與計算的值（不會將 null 列入考慮）。

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

一般計數會計算 null： 

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
