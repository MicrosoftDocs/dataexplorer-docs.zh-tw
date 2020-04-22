---
title: 彙總運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的匯總運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.openlocfilehash: ed1808f173d0f779c84f9405987d7395de833120
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663209"
---
# <a name="summarize-operator"></a>summarize 運算子

產生資料表來彙總輸入資料表的內容。

```kusto
T | summarize count(), avg(price) by fruit, supplier
```

顯示每個供應商每個水果的數量和平均價格的表。 水果和供應商的每個不同組合的產出都有一行。 輸出列顯示計數、平均價格、水果和供應商。 其他所有輸入資料行則會遭到忽略。

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

顯示有多少項目的價格落在 [0,10.0]、[10.0,20.0] 等依此類推的間隔中的資料表。 此範例有一個用於放置計數的資料行，以及一個用於放置價格範圍的資料行。 其他所有輸入資料行則會遭到忽略。

**語法**

*T* `| summarize` *Column*`=`=`,`欄位 [*聚合*] [ ][`by` *Column*`=`]`,`列 [*組式表示式*]

**引數**

* ** 結果資料行的選擇性名稱。 預設值為衍生自運算式的名稱。
* *彙總:* 對[聚合函數](summarizeoperator.md#list-of-aggregation-functions)(`count()`如`avg()`或 )的調用,列名稱為參數。 請參閱 [彙總函式清單](summarizeoperator.md#list-of-aggregation-functions)。
* ** 可提供一組相異值的資料行運算式。 它通常是已提供一組受限值的資料行名稱，或是以數值或時間資料行做為引數的 `bin()` 。 

> [!NOTE]
> 輸入表格時, 輸出目前無法使用*群組表示式*:
>
> * 如果未提供*GroupExpression,* 則輸出將是單個(空)行。
> * 如果提供*組運算*式 ,則輸出將沒有行。

**傳回**

輸入資料列會各自分組到具有相同 `by` 運算式值的群組。 然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。 結果會包含 `by` 資料行，而且每個經過計算的彙總至少會佔有一個資料行。 (某些彙總函式會傳回多個資料行)。

結果具有與`by`值的不同組合(可能是零)一樣多的行。 如果沒有提供組鍵,則結果具有單個記錄。

要匯總數值範圍,請使用`bin()`將範圍減小到離散值。

> [!NOTE]
> * 雖然您可以為彙總與群組運算式提供任意運算式，但更有效率的方法是使用簡單的資料行名稱，或對數值資料行套用 `bin()` 。
> * 不再支援日期時間列的自動小時箱。 改用顯式分箱。 例如： `summarize by bin(timestamp, 1h)` 。

## <a name="list-of-aggregation-functions"></a>彙總函式清單

|函式|描述|
|--------|-----------|
|[any()](any-aggfunction.md)|傳回群組的隨機非空值|
|[anyif()](anyif-aggfunction.md)|傳回群組的隨機非空值(帶謂詞)|
|[arg_max()](arg-max-aggfunction.md)|最大化參數時傳回一個或多個運算式|
|[arg_min()](arg-min-aggfunction.md)|最小化參數時傳回一個或多個運算式|
|[avg()](avg-aggfunction.md)|傳回整個群組的平均值|
|[avgif()](avgif-aggfunction.md)|回整個組的平均值(帶謂詞)|
|[binary_all_and](binary-all-and-aggfunction.md)|使用群組的二進位檔案`AND`傳回集合值|
|[binary_all_or](binary-all-or-aggfunction.md)|使用群組的二進位檔案`OR`傳回集合值|
|[binary_all_xor](binary-all-xor-aggfunction.md)|使用群組的二進位檔案`XOR`傳回集合值|
|[buildschema()](buildschema-aggfunction.md)|傳回`dynamic`允許輸入所有值的最小架構|
|[count()](count-aggfunction.md)|傳回群組的計數|
|[countif()](countif-aggfunction.md)|傳回具有群組的謂詞的計數|
|[dcount()](dcount-aggfunction.md)|傳回元件元素的近似不同計數|
|[dcountif()](dcountif-aggfunction.md)|回回元件元素的近似不同計數(帶謂詞)|
|[make_bag()](make-bag-aggfunction.md)|傳回群組的動態值的屬性套件|
|[make_bag_if()](make-bag-if-aggfunction.md)|傳回群組中的動態值的屬性套件(包含字)|
|[make_list()](makelist-aggfunction.md)|傳回群組內所有值的清單|
|[make_list_if()](makelistif-aggfunction.md)|傳回群組內所有值的清單(含標題 )|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|傳回群組內所有值的清單,包括空值|
|[make_set()](makeset-aggfunction.md)|傳回群組中的一個不同值|
|[make_set_if()](makesetif-aggfunction.md)|傳回群組的一組不同值(帶謂詞)|
|[max()](max-aggfunction.md)|傳回整個群組的最大值|
|[maxif()](maxif-aggfunction.md)|傳回整個群組的最大值(帶謂詞)|
|[min()](min-aggfunction.md)|傳回整個群組的最小值|
|[minif()](minif-aggfunction.md)|傳回整個群組的最小值(帶謂詞)|
|[percentiles()](percentiles-aggfunction.md)|傳回元件的百分位數近似值|
|[percentiles_array()](percentiles-aggfunction.md)|傳回元件的百分位數近似值|
|[百分位數sw()](percentiles-aggfunction.md)|傳回元件的加權百分位數近似值|
|[percentilesw_array()](percentiles-aggfunction.md)|傳回元件的加權百分位數近似值|
|[stdev()](stdev-aggfunction.md)|傳回整個組的標準偏差|
|[stdevif()](stdevif-aggfunction.md)|傳回整個群組的標準差(帶謂詞)|
|[sum()](sum-aggfunction.md)|傳回有群組的元素的總和|
|[sumif()](sumif-aggfunction.md)|傳回具有群組的元素的總和(帶謂詞)|
|[variance()](variance-aggfunction.md)|傳回整個群組的差異|
|[varianceif()](varianceif-aggfunction.md)|傳回整個群組的方差(帶謂詞)|

## <a name="aggregates-default-values"></a>彙總預設值

下表總結了集合的預設值:

運算子       |預設值                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    空動態陣列 (*)          
 All others          |   null                           

 在包含空值的實體上使用這些聚合時,將忽略空值,並且不會參與計算(請參閱下面的示例)。

## <a name="examples"></a>範例

![alt 文字](./Images/aggregations/01.png "01")

**範例**

確定表中和 表`ActivityType`中`CompletionStatus`有哪些 唯一組合。 沒有聚合函數,只有分組鍵。 輸出將顯示這些結果的欄:

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

**範例**

在"活動"表中尋找所有記錄的最小和最大時間戳。 由於沒有 group-by 子句，因此輸出中只有一個資料列︰

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

**範例**

為每個大陸創建一行,顯示活動發生的城市計數。 由於「大陸」的值很少,因此「by」子句不需要分組函數:

    Activities | summarize cities=dcount(city) by continent

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


**範例**

下面的範例計算每個活動類型的直方圖。 由於`Duration`有許多值,因此`bin`使用將其值分組為 10 分鐘間隔:

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

當運算符的`summarize`輸入至少有一個空的逐組鍵時,結果也為空。

當運算子輸入`summarize`沒有空的群組鍵時,結果是 : 中使用的集合的預設值`summarize`:

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

聚合 avg 求和所有非空值,並僅計算參與計算的人員(不會考慮 null)。

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

一般計數將計算 null: 

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
