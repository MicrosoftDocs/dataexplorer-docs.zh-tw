---
title: Kusto 分割 & 撰寫中繼匯總結果-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中分割及撰寫匯總的中繼結果。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 30d4f6bd315b5a32c67570ab16b9abc3160f0177
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954479"
---
# <a name="using-hll-and-tdigest"></a>使用 hll() 和 tdigest()

假設您想要在過去七天內每天計算不同使用者的計數。 您可以 `summarize dcount(user)` 一天執行一次，並將範圍篩選至過去七天。 這種方法的效率不佳，因為每次執行計算時，會有六天與先前的計算重迭。 您也可以計算每天的匯總，然後合併這些匯總。 這個方法會要求您「記住」最後六個結果，但效率更高。

如所述，資料分割查詢很容易用於簡單的匯總，例如 `count()` 和 `sum()` 。 它也適用于複雜的匯總，例如 `dcount()` 和 `percentiles()` 。 本主題說明 Kusto 如何支援這類計算。

下列範例示範如何使用 `hll` / `tdigest` 和示範在某些情況下，使用這些命令的效能非常高效：

> [!NOTE]
> 在某些情況下，或彙總函式所產生的動態物件 `hll` `tdigest` 可能會很大，而且在編碼原則中會超過預設的 MaxValueSize 屬性。 如果是，物件將會內嵌為 null。
> 例如，使用精確度層級4來保存函式的輸出時 `hll` ，物件的大小 `hll` 會超過預設 MaxValueSize，也就是1mb。
> 若要避免這個問題，請修改資料行的編碼原則，如下列範例所示。

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1.0000524520874|

套用這類原則之前，請先將此物件擷取到資料表中，以內嵌 null：

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

```kusto
MyTable
| project isempty(hll_x)
```

| 資料行1 |
|---------|
| 1       |

若要避免擷取 null，請使用特殊的編碼原則類型 `bigobject` ，此類型會覆寫 `MaxValueSize` 為 2 MB，如下所示：

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```

現在將值擷取到相同的資料表：

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

成功內嵌第二個值： 

```kusto
MyTable
| project isempty(hll_x)
```

|資料行1|
|---|
|1|
|0|


## <a name="example-count-with-binned-timestamp"></a>範例：具有分類收納時間戳記的計數

其中有一個資料表， `PageViewsHllTDigest` 其中包含 `hll` 每小時查看的頁面值。 您希望這些值分類收納到 `12h` 。 `hll`使用 `hll_merge()` 彙總函式來合併值，並將時間戳記分類收納至 `12h` 。 使用函數傳回 `dcount_hll` 最後的 `dcount` 值：

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|時間戳記|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 12：00：00.0000000|20056275|
|2016-05-02 00：00：00.0000000|38797623|
|2016-05-02 12：00：00.0000000|39316056|
|2016-05-03 00：00：00.0000000|13685621|

若要將時間戳記分類為 `1d` ：

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|時間戳記|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 00：00：00.0000000|20056275|
|2016-05-02 00：00：00.0000000|64135183|
|2016-05-03 00：00：00.0000000|13685621|

相同的查詢可能會透過的值來完成 `tdigest` ，這表示 `BytesDelivered` 每小時的：

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|時間戳記|`percentile_tdigest_merged_tdigests`|
|---|---|
|2016-05-01 12：00：00.0000000|170200|
|2016-05-02 00：00：00.0000000|152975|
|2016-05-02 12：00：00.0000000|181315|
|2016-05-03 00：00：00.0000000|146817|
 
## <a name="example-temporary-table"></a>範例：臨時表

如果資料集太大，您需要對資料集執行定期查詢，但要執行定期查詢來計算 [`percentile()`](percentiles-aggfunction.md) 或 [`dcount()`](dcount-aggfunction.md) 超過大型資料集，就會達到 Kusto 限制。

::: zone pivot="azuredataexplorer"

若要解決這個問題，您可以將新加入的資料新增到臨時表中，或在所需 `hll` `tdigest` 的作業是， [`hll()`](hll-aggfunction.md) `dcount` 或 [`tdigest()`](tdigest-aggfunction.md) 使用或時所需的作業為百分位數時使用 [`set/append`](../../ingest-data-overview.md) [`update policy`](../management/updatepolicy.md) 。 在此情況下，或的中繼 `dcount` 結果 `tdigest` 會儲存至另一個資料集，此資料集應該小於目標大型資料集。

::: zone-end

::: zone pivot="azuremonitor"

若要解決這個問題， `hll` `tdigest` [`hll()`](hll-aggfunction.md) 當所需的作業是時，新加入的資料可能會新增至臨時表，或是使用的值 `dcount` 。 在此情況下，的中繼結果 `dcount` 會儲存至另一個資料集，此資料集應該小於目標大型資料集。

::: zone-end

當您需要取得這些值的最終結果時，查詢可能會使用 `hll` / `tdigest` 合併： [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md) 。 然後，在取得合併的值之後， [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) 可能會在這些合併的值上叫用，以取得或百分位數的最終結果 `dcount` 。

假設有一個資料表 PageViews，其中的資料會每天內嵌，您每天都要在日期 = datetime (2016-05-01 18：00： 00.0000000) 計算每分鐘查看的相異頁面計數。

執行下列查詢：

```kusto
PageViews   
| where Timestamp > datetime(2016-05-01 18:00:00.0000000)
| summarize percentile(BytesDelivered, 90), dcount(Page,2) by bin(Timestamp, 1d)
```

|時間戳記|percentile_BytesDelivered_90|dcount_Page|
|---|---|---|
|2016-05-01 00：00：00.0000000|83634|20056275|
|2016-05-02 00：00：00.0000000|82770|64135183|
|2016-05-03 00：00：00.0000000|72920|13685621|

此查詢會在您每次執行此查詢時匯總所有值 (例如，如果您想要一天) 執行多次。

如果您儲存 `hll` 和 `tdigest` 值 (這是 `dcount` 和百分位數的中繼結果) 到臨時表中， `PageViewsHllTDigest` 使用更新原則或 set/append 命令，您可能只會合並值，然後使用 `dcount_hll` / `percentile_tdigest` 下列查詢：

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|時間戳記|`percentile_tdigest_merge_tdigests_tdigestBytesDel`|`dcount_hll_hll_merge_hllPage`|
|---|---|---|
|2016-05-01 00：00：00.0000000|84224|20056275|
|2016-05-02 00：00：00.0000000|83486|64135183|
|2016-05-03 00：00：00.0000000|72247|13685621|

此查詢的效能會更高，因為它會在較小的資料表上執行。 在此範例中，第一個查詢會執行超過 ~ 215M 筆記錄，而第二個查詢則只會執行32記錄：

## <a name="example-intermediate-results"></a>範例：中繼結果

保留查詢。
假設您有一個資料表，摘要說明每個維琪百科頁面的觀看時間 (範例大小是1千萬個) ，而您想要尋找每個值1：1：1：1：1：1：1日和) < (2：
  
簡單的方式是使用聯結和摘要運算子：

```kusto
// Get the total pages viewed each day
let totalPagesPerDay = PageViewsSample
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
// Join the table to itself to get a grid where 
// each row shows foreach page1, in which two dates
// it was viewed.
// Then count the pages between each two dates to
// get how many pages were viewed between date1 and date2.
PageViewsSample
| summarize by Page, Day1 = startofday(Timestamp)
| join kind = inner
(
    PageViewsSample
    | summarize by Page, Day2 = startofday(Timestamp)
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

|第 1 天|第 2 天|百分比|
|---|---|---|
|2016-05-01 00：00：00.0000000|2016-05-02 00：00：00.0000000|34.0645725975255|
|2016-05-01 00：00：00.0000000|2016-05-03 00：00：00.0000000|16.618368960101|
|2016-05-02 00：00：00.0000000|2016-05-03 00：00：00.0000000|14.6291376489636|

 
上述查詢花費 ~ 18 秒。

當您使用、和函式時 [`hll()`](hll-aggfunction.md) [`hll_merge()`](hll-merge-aggfunction.md) [`dcount_hll()`](dcount-hllfunction.md) ，對等查詢將會在 ~ 1.3 秒後結束，並顯示函式將 `hll` 上述查詢加速超過 ~ 14 次：

```kusto
let Stats=PageViewsSample | summarize pagehll=hll(Page, 2) by day=startofday(Timestamp); // saving the hll values (intermediate results of the dcount values)
let day0=toscalar(Stats | summarize min(day)); // finding the min date over all dates.
let dayn=toscalar(Stats | summarize max(day)); // finding the max date over all dates.
let daycount=tolong((dayn-day0)/1d); // finding the range between max and min
Stats
| project idx=tolong((day-day0)/1d), day, pagehll
| mv-expand pidx=range(0, daycount) to typeof(long)
// Extend the column to get the dcount value from hll'ed values for each date (same as totalPagesPerDay from the above query)
| extend key1=iff(idx < pidx, idx, pidx), key2=iff(idx < pidx, pidx, idx), pages=dcount_hll(pagehll)
// For each two dates, merge the hll'ed values to get the total dcount over each two dates, 
// This helps to get the pages viewed in both date1 and date2 (see the description below about the intersection_size)
| summarize (day1, pages1)=arg_min(day, pages), (day2, pages2)=arg_max(day, pages), union_size=dcount_hll(hll_merge(pagehll)) by key1, key2
| where day2 > day1
// To get pages viewed in date1 and also date2, look at the merged dcount of date1 and date2, subtract it from pages of date1 + pages on date2.
| project pages1, day1,day2, intersection_size=(pages1 + pages2 - union_size)
| project day1, day2, Percentage = intersection_size*100.0 / pages1
```

|day1.ps1|day2.ps1|百分比|
|---|---|---|
|2016-05-01 00：00：00.0000000|2016-05-02 00：00：00.0000000|33.2298494510578|
|2016-05-01 00：00：00.0000000|2016-05-03 00：00：00.0000000|16.9773830213667|
|2016-05-02 00：00：00.0000000|2016-05-03 00：00：00.0000000|14.5160020350006|

> [!NOTE] 
> 由於函數的錯誤，查詢的結果不是100% 精確 `hll` 。 如需錯誤的詳細資訊，請參閱 [`dcount()`](dcount-aggfunction.md) 。
