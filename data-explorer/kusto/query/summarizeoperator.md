---
title: 摘要操作員-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的摘要操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.localizationpriority: high
ms.openlocfilehash: 810eba264c717d156f74b9958edecb712d58a4fd
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513194"
---
# <a name="summarize-operator"></a>summarize 運算子

產生資料表來彙總輸入資料表的內容。

```kusto
Sales | summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime)
```

傳回資料表，其中包含多少銷售交易和每個水果和銷售月份的總金額。
輸出資料行會顯示交易記錄的交易計數、交易數量、水果數量，以及記錄月份開頭的日期時間。

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

顯示有多少項目的價格落在 [0,10.0]、[10.0,20.0] 等依此類推的間隔中的資料表。 此範例有一個用於放置計數的資料行，以及一個用於放置價格範圍的資料行。 其他所有輸入資料行則會遭到忽略。

## <a name="syntax"></a>語法

*T* `| summarize` [[*column* `=` ] *匯總* [ `,` ...]] [[資料 `by` *行* `=` ] *GroupExpression* [ `,` ...]]

## <a name="arguments"></a>引數

*  結果資料行的選擇性名稱。 預設值為衍生自運算式的名稱。
* *匯總：*[aggregation function](summarizeoperator.md#list-of-aggregation-functions) `count()` 使用資料 `avg()` 行名稱做為引數的彙總函式（例如或）的呼叫。 請參閱 [彙總函式清單](summarizeoperator.md#list-of-aggregation-functions)。
* *GroupExpression：* 可以參考輸入資料的純量運算式。
  輸出的記錄會與所有群組運算式的相異值一樣多。

> [!NOTE]
> 當輸入資料表是空的時，輸出會根據是否使用 *GroupExpression* 而定：
>
> * 如果未提供 *GroupExpression* ，則輸出將會是單一 (空白) 資料列。
> * 如果有提供 *GroupExpression* ，輸出就不會有資料列。

## <a name="returns"></a>傳回

輸入資料列會各自分組到具有相同 `by` 運算式值的群組。 然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。 結果會包含 `by` 資料行，而且每個經過計算的彙總至少會佔有一個資料行。 (某些彙總函式會傳回多個資料行)。

結果具有不同值組合的資料列數目 `by` (可能是零) 。 如果未提供任何群組索引鍵，則結果會有單一記錄。

若要匯總數值的範圍，請使用 `bin()` 將範圍減少為離散值。

> [!NOTE]
> * 雖然您可以為彙總與群組運算式提供任意運算式，但更有效率的方法是使用簡單的資料行名稱，或對數值資料行套用 `bin()` 。
> * 不再支援 datetime 資料行的自動每小時。 請改用明確的分類收納。 例如： `summarize by bin(timestamp, 1h)` 。

## <a name="list-of-aggregation-functions"></a>彙總函式的清單

|函式|描述|
|--------|-----------|
|[any()](any-aggfunction.md)|傳回群組的隨機非空白值|
|[anyif()](anyif-aggfunction.md)|使用述詞傳回群組 (的隨機非空白值) |
|[arg_max()](arg-max-aggfunction.md)|當引數最大化時，傳回一或多個運算式|
|[arg_min()](arg-min-aggfunction.md)|當引數最小化時，傳回一或多個運算式|
|[avg()](avg-aggfunction.md)|傳回整個群組的平均值|
|[avgif()](avgif-aggfunction.md)|使用述詞傳回整個群組 (的平均值) |
|[binary_all_and](binary-all-and-aggfunction.md)|使用群組的二進位檔傳回匯總值 `AND`|
|[binary_all_or](binary-all-or-aggfunction.md)|使用群組的二進位檔傳回匯總值 `OR`|
|[binary_all_xor](binary-all-xor-aggfunction.md)|使用群組的二進位檔傳回匯總值 `XOR`|
|[buildschema()](buildschema-aggfunction.md)|傳回認可輸入所有值的最基本架構 `dynamic`|
|[count()](count-aggfunction.md)|傳回群組的計數|
|[countif()](countif-aggfunction.md)|傳回包含群組述詞的計數|
|[dcount()](dcount-aggfunction.md)|傳回群組元素的近似相異計數|
|[dcountif()](dcountif-aggfunction.md)|傳回與述詞 (之群組元素的近似相異計數) |
|[make_bag()](make-bag-aggfunction.md)|傳回群組內動態值的屬性包|
|[make_bag_if()](make-bag-if-aggfunction.md)|使用述詞，傳回群組 (內動態值的屬性包) |
|[make_list()](makelist-aggfunction.md)|傳回群組中所有值的清單。|
|[make_list_if()](makelistif-aggfunction.md)|使用述詞傳回群組 (內所有值的清單) |
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|傳回群組中所有值的清單，包括 null 值|
|[make_set()](makeset-aggfunction.md)|傳回群組內的一組相異值|
|[make_set_if()](makesetif-aggfunction.md)|使用述詞，傳回群組 (內的一組相異值) |
|[max()](max-aggfunction.md)|傳回整個群組的最大值|
|[maxif()](maxif-aggfunction.md)|使用述詞傳回跨群組 (的最大值) |
|[min()](min-aggfunction.md)|傳回整個群組的最小值|
|[minif()](minif-aggfunction.md)|使用述詞傳回跨群組 (的最小值) |
|[percentiles()](percentiles-aggfunction.md)|傳回群組的百分位數近似值|
|[percentiles_array ( # B1 ](percentiles-aggfunction.md)|傳回群組的百分位數近似|
|[percentilesw ( # B1 ](percentiles-aggfunction.md)|傳回群組的加權百分位數近似值|
|[percentilesw_array ( # B1 ](percentiles-aggfunction.md)|傳回群組的加權百分位數近似|
|[stdev()](stdev-aggfunction.md)|傳回整個群組的標準差|
|[stdevif()](stdevif-aggfunction.md)|使用述詞傳回群組 (之間的標準差) |
|[sum()](sum-aggfunction.md)|傳回群組內元素的總和|
|[sumif()](sumif-aggfunction.md)|使用述詞傳回群組 (內元素的總和) |
|[variance()](variance-aggfunction.md)|傳回整個群組的變異數|
|[varianceif()](varianceif-aggfunction.md)|使用述詞傳回群組 (之間的變異數) |

## <a name="aggregates-default-values"></a>匯總預設值

下表摘要列出匯總的預設值：

運算子       |預設值                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    空的動態陣列 ( [] )           
 All others          |   null                           

 在包含 null 值的實體上使用這些匯總時，將會忽略 null 值，而且不會參與計算 (請參閱下面的範例) 。

## <a name="examples"></a>範例

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="依水果和供應商摘要定價":::

## <a name="example-unique-combination"></a>範例：唯一的組合

判斷 `ActivityType` 和資料表中有哪些唯一的組合 `CompletionStatus` 。 沒有任何彙總函式，只是分組依據的索引鍵。 輸出只會顯示這些結果的資料行：

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

尋找活動資料表中所有記錄的最小和最大時間戳記。 由於沒有 group-by 子句，因此輸出中只有一個資料列︰

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example-distinct-count"></a>範例：相異計數

為每個大陸建立一個資料列，顯示發生活動的城市計數。 因為「大陸」有幾個值，所以 ' by ' 子句中不需要任何群組函數：

```kusto
Activities | summarize cities=dcount(city) by continent
```

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example-histogram"></a>範例：長條圖

下列範例會計算每個活動類型的長條圖。 由於 `Duration` 有許多值，因此請使用 `bin` 將其值分組為10分鐘的間隔：

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

當運算子的輸入 `summarize` 至少有一個空白群組索引鍵時，它的結果也會是空的。

當運算子的輸入沒有 `summarize` 空白的分組依據索引鍵時，結果會是中使用的匯總預設值 `summarize` ：

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

匯總平均加總非 null，並只計算參與計算 (不會將 null 納入帳戶) 。

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
