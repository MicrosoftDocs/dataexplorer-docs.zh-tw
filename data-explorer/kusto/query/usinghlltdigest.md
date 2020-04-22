---
title: 分割區與組合聚合的中間結果 - Azure 資料資源管理員 ( Azure) 的資源管理員 。微軟文件
description: 本文介紹了 Azure 數據資源管理器中聚合的中間結果的分區和組合。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1392105e4b5d99f2273b686ed74a161750ee6f8c
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81662954"
---
# <a name="partitioning-and-composing-intermediate-results-of-aggregations"></a>彙總的中間結果的分割區與組合

假設您要計算過去七天、每天的不同使用者的計數。 一種方法是每天運行`summarize dcount(user)`一次,其範圍被過濾到過去七天。 這效率很低,因為每次運行計算時,都會與以前的計算重疊六天。 另一個選項是計算每天的一些聚合,然後以有效的方式組合這些聚合。 此選項要求您「記住」最後六個結果,但它的效率要高得多。

對於簡單的聚合(如 count() 和 sum()))等,此類劃分查詢將很容易。 但是,還可以對更複雜的聚合(如 dcount() 和百分位數()執行它們。 本主題介紹 Kusto 如何支援此類計算。

以下範例展示如何使用 hll/tdigest,並展示在某些情況下使用這些方法可能比在其他方面更具有效能:

> [!NOTE]
> 在某些情況下,或`hll``tdigest`聚合函數生成的動態物件可能很大,並且超過編碼策略中的預設 MaxValueSize 屬性。 如果是這樣,則物件將引入為 null。
例如,當保留精度級別`hll`4 的函數輸出時,hll 物件的大小超過預設的 MaxValueSize,即 1MB。

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1.0000524520874|

在應用此類原則之前將引入到表格中將引入 null:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

```kusto
MyTable
| project isempty(hll_x)
```

| Column1 |
|---------|
| 1       |

為了避免這種情況,請使用特殊的編碼策略類型,該`bigobject`類型將 MaxValueSize 覆蓋到 2MB,如下所示:

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```



因此,現在將值引入到上面的同一表中:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```
成功引入第二個值: 

```kusto
MyTable
| project isempty(hll_x)
```

|Column1|
|---|
|1|
|0|


**範例**

假設有一個表,PageViewsHllTDigest,它具有每小時查看的頁面的 hll 值。 目的是將這些值(但裝箱到`12h`)使用 hll_merge()聚合函數將 hll`12h`值合併到 。 然後,呼叫函數dcount_hll取得最終 dcount 值:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|時間戳記|dcount_hll_merged_hll|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

甚至對於`1d`: 的裝箱時間戳:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|時間戳記|dcount_hll_merged_hll|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

 在 tdigest 的值上也可以執行相同的工作,這些值表示每小時傳遞的位元組:

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|時間戳記|percentile_tdigest_merged_tdigests|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
**範例**

庫sto 限制是使用過大的數據集達到的,需要對數據集運行定期查詢,但運行常規查詢以[`percentile()`](percentiles-aggfunction.md)計算[`dcount()`](dcount-aggfunction.md)或計算這些大型數據集。

::: zone pivot="azuredataexplorer"

為了解決[`hll()`](hll-aggfunction.md)此問題,當所需操作為 dcount 或所需的操作使用百[`tdigest()`](tdigest-aggfunction.md)[`set/append`](../management/data-ingestion/index.md)分位數 時,新添加的數據可以作為 hll 或 tdigest[`update policy`](../management/updatepolicy.md)值添加到臨時表中 ,或者,在這種情況下,dcount 或 tdigest 的中間結果將保存到另一個數據集中,該數據集應小於目標大數據集。

::: zone-end

::: zone pivot="azuremonitor"

為了解決此問題,新添加的數據可能會作為 hll 或 tdigest 值添加到臨時[`hll()`](hll-aggfunction.md)表中,當 所需的操作為 dcount 時使用,在這種情況下,dcount 的中間結果將保存到另一個數據集中,該數據集應小於目標大數據集。

::: zone-end

當您需要取得這些值的最終結果時,查詢可能使用[`hll-merge()`](hll-merge-aggfunction.md)/[`tdigest_merge()`](tdigest-merge-aggfunction.md)hll/tdigest 合併: 然後,在獲取合併的[`percentile_tdigest()`](percentile-tdigestfunction.md) / [`dcount_hll()`](dcount-hllfunction.md)值後, 可以調用這些合併的值,以獲得 dcount 或百分位數的最終結果。

假設有一個表,PageViews,每天將數據引入其中,您希望計算比日期晚於日期和日期時間(2016-05-01 18:00:0000000)每天查看的每分鐘數頁的不同計數。

執行以下查詢 :

```kusto
PageViews   
| where Timestamp > datetime(2016-05-01 18:00:00.0000000)
| summarize percentile(BytesDelivered, 90), dcount(Page,2) by bin(Timestamp, 1d)
```

|時間戳記|percentile_BytesDelivered_90|dcount_Page|
|---|---|---|
|2016-05-01 00:00:00.0000000|83634|20056275|
|2016-05-02 00:00:00.0000000|82770|64135183|
|2016-05-03 00:00:00.0000000|72920|13685621|

每次運行此查詢時,此查詢都會聚合所有值(例如,如果要每天運行多次)。

如果將 hll 和 tdigest 值(即 dcount 和百分位數的中間結果)儲存到暫時表 PageViewsHllTDigest 中,請使用更新策略或設定/追加命令,則只能合併這些值,然後使用以下查詢使用dcount_hll/percentile_tdigest:

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|時間戳記|percentile_tdigest_merge_tdigests_tdigestBytesDel|dcount_hll_hll_merge_hllPage|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

這應該更具性能性,並且查詢通過較小的表運行(在此示例中,第一個查詢運行超過 ^215M 記錄,而第二個查詢僅運行 32 條記錄以上)。

**範例**

保留查詢。
假設您有一個表格,匯總了每次查看每個維琪百科頁面時(樣本大小為 10M),並且您希望為每個日期 1 日期查找日期 1 和日期 2 中查看的頁面相對於日期 1 時查看的頁面(日期 1 < 日期 2)的百分比。
  
使用聯接和匯總運算子的瑣碎方式:

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
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|

 
上面的查詢花了18秒。

使用 與[`hll()`](hll-aggfunction.md)的[`hll_merge()`](hll-merge-aggfunction.md)[`dcount_hll()`](dcount-hllfunction.md)函數 時 ,等效查詢將在 ±1.3 秒後結束,並顯示 hll 函數將上述查詢的速度提高 14 倍:

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

|第1天|第二天|百分比|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> 由於 hll 函數的錯誤,查詢結果不是 100% 準確。(有關[`dcount()`](dcount-aggfunction.md)錯誤的詳細資訊,請參閱)。
