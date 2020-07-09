---
title: 資料分割原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的資料分割原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: 7f299a730b451f608e0d2c81fc78565d515fc029
ms.sourcegitcommit: bcb87ed043aca7c322792c3a03ba0508026136b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86127304"
---
# <a name="data-partitioning-policy"></a>資料分割原則

分割原則會針對特定資料表定義是否應該分割[範圍（資料分區）](../management/extents-overview.md) 。

原則的主要目的是要改善已知的查詢效能，以縮小分割資料行中的值資料集，或在高基數位符串資料行上進行匯總/聯結。 原則也可能會導致資料的壓縮變得更好。

> [!CAUTION]
> 在可定義原則的資料表數目上，沒有已設定的硬式編碼限制。 不過，每個額外的資料表都會增加在叢集節點上執行之背景資料分割程式的負擔。 這可能會導致使用更多的叢集資源。 如需詳細資訊，請參閱[監視](#monitoring)和[容量](#capacity)。

## <a name="partition-keys"></a>資料分割索引鍵

支援下列幾種資料分割索引鍵。

|種類                                                   |資料行類型 |磁碟分割內容                    |資料分割值                                        |
|-------------------------------------------------------|------------|----------------------------------------|----------------------|
|[雜湊](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[統一範圍](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>雜湊分割區索引鍵

> [!NOTE]
> `string`**只有**當大部分的查詢都使用相等篩選（ `==` ， `in()` ），或在 `string` *大型維度*（1千萬個或更高的基數）的特定類型資料行上匯總/聯結（例如 `application_ID` 、 `tenant_ID` 或） `user_ID` 時，才適合在資料表的類型資料行上套用雜湊資料分割索引鍵。

* 雜湊模數函數可用來分割資料。
* 屬於相同資料分割的所有同質（資料分割）範圍都會指派給相同的資料節點。
* 同質（已分割）範圍中的資料是依雜湊資料分割索引鍵來排序。
  * 如果資料表上有定義雜湊資料分割索引鍵，您就不需要將它包含在資料[列順序原則](roworderpolicy.md)中。
* 使用[隨機播放策略](../query/shufflequery.md)的查詢，以及 `shuffle key` 資料表的雜湊分割區索引鍵所使用的，其 `join` `summarize` 預期會 `make-series` 執行得更好，因為在叢集節點之間移動所需的資料量會大幅降低。

#### <a name="partition-properties"></a>磁碟分割內容

* `Function`這是要使用的雜湊模數函數名稱。
  * 支援的值： `XxHash64` 。
* `MaxPartitionCount`這是每個時間週期內所要建立的分割區數目上限（雜湊模數函數的模數引數）。
  * 支援的值在範圍內 `(1,1024]` 。
    * 此值應為：
      * 大於叢集中的節點數目
      * 小於資料行的基數。
    * 值愈高，叢集節點上的資料分割程式的額外負荷就越大，而且每個時間週期的範圍數目越高。
    * 我們建議您從的值開始 `256` 。
      * 根據上述考慮來調整值，或根據查詢效能的好處，或在內嵌後分割資料的額外負荷。
* `Seed`這是用來隨機化雜湊值的值。
  * 此值應為正整數。
  * 建議的值是 `1` ，如果未指定，則為預設值。

#### <a name="example"></a>範例

具有類型之資料行的雜湊資料分割索引鍵 `string` `tenant_id` 。
它會使用 `XxHash64` 雜湊函式，並搭配 `MaxPartitionCount` 的 `256` 和預設 `Seed` 值 `1` 。

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>統一範圍 datetime 資料分割索引鍵

> [!NOTE] 
> `datetime`只有在資料表中內嵌的資料不太可能根據這個資料行進行排序時，**才**適合在資料表中的類型資料行上套用統一範圍 datetime 資料分割索引鍵。

在這種情況下，在範圍之間重新資料可能會很有説明，因為每個範圍最後都會包含限制時間範圍內的記錄。 這會導致該資料行上的篩選準則在 `datetime` 查詢時更有效率。

* 使用的資料分割函數是[bin_at （）](../query/binatfunction.md) ，無法自訂。

#### <a name="partition-properties"></a>磁碟分割內容

* `RangeSize`這是一個純 `timespan` 量常數，表示每個 datetime 資料分割的大小。
  我們建議您：
  * 開頭為值 `1.00:00:00` （一天）。
  * 請不要設定較短的值，因為它可能會導致資料表擁有無法合併的大量小型範圍。
* `Reference`這是一個純 `datetime` 量常數，根據要對齊的日期時間分割區，指出固定的時間點。
  * 我們建議您從開始 `1970-01-01 00:00:00` 。
  * 如果日期時間分割區索引鍵有值的記錄 `null` ，其資料分割值會設為的值 `Reference` 。

#### <a name="example"></a>範例

程式碼片段會在名為的具類型資料行上顯示統一的 datetime 範圍分割區索引鍵 `datetime` `timestamp` 。
它會使用 `datetime(1970-01-01)` 作為其參考點， `1d` 每個分割區的大小為。

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00"
  }
}
```

## <a name="the-policy-object"></a>原則物件

根據預設，資料表的資料分割原則是 `null` ，在此情況下，資料表中的資料不會分割。

資料分割原則具有下列主要屬性：

* **PartitionKeys**：
  * 分割區索引[鍵](#partition-keys)的集合，定義如何分割資料表中的資料。
  * 資料表最多可以有 `2` 下列其中一個選項的分割區索引鍵。
    * 一個[雜湊資料分割索引鍵](#hash-partition-key)。
    * 一個[統一範圍的 datetime 資料分割索引鍵](#uniform-range-datetime-partition-key)。
    * 一個[雜湊資料分割索引鍵](#hash-partition-key)和一個[統一範圍的 datetime 資料分割索引鍵](#uniform-range-datetime-partition-key)。
  * 每個分割區索引鍵都有下列屬性：
    * `ColumnName`： `string ` -資料行的名稱，將會根據該資料分割資料。
    * `Kind`： `string` -要套用的資料分割類型（ `Hash` 或 `UniformRange` ）。
    * `Properties`： `property bag` -根據完成的資料分割定義參數。

* **EffectiveDateTime**：
  * 原則生效的 UTC 日期時間。
  * 這是選用屬性。 如果未指定，原則會在套用原則之後，于資料內嵌上生效。
  * 任何可能因為保留而卸載的非同質（非資料分割）範圍都會被分割程式忽略，因為它們的建立時間在資料表的有效虛刪除期間的90% 之前。

### <a name="example"></a>範例

具有兩個分割區索引鍵的資料分割原則物件。
1. 具有類型之資料行的雜湊資料分割索引鍵 `string` `tenant_id` 。
    - 它會使用 `XxHash64` 雜湊函式，其為 `MaxPartitionCount` 256，而預設值為 `Seed` `1` 。
1. 在名為的類型資料行上的統一 datetime 範圍資料分割索引鍵 `datetime` `timestamp` 。
    - 它會使用 `datetime(1970-01-01)` 作為其參考點， `1d` 每個分割區的大小為。

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1
      }
    },
    {
      "ColumnName": "timestamp",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00"
      }
    }
  ]
}
```

### <a name="additional-properties"></a>其他屬性

下列屬性可以定義為原則的一部分，但是選擇性的，建議您不要變更它們。

* **MinRowCountPerOperation**：
  * 單一資料分割作業之來源範圍的資料列計數總和的最小目標。
  * 這是選用屬性。 其預設值為 `0`。

* **MaxRowCountPerOperation**：
  * 單一資料分割作業之來源範圍的資料列計數總和的最大目標。
  * 這是選用屬性。 其預設值為 `0` ，預設目標為5000000記錄。
    * 如果您看到資料分割作業耗用的記憶體或 CPU 數量非常龐大，則您可以設定低於5M 的值。 如需詳細資訊，請參閱[監視](#monitoring)。

## <a name="notes"></a>注意

### <a name="the-data-partitioning-process"></a>資料分割進程

* 資料分割會在叢集中做為內嵌後背景進程執行。
  * 持續內嵌到的資料表預期一律會有資料的「結尾」，但尚未分割（非同質範圍）。
* 不論原則中的屬性值為何，資料分割都只會在經常性存取範圍上執行 `EffectiveDateTime` 。
  * 如果需要分割冷範圍，您將需要暫時調整快取[原則](cachepolicy.md)。

#### <a name="monitoring"></a>監視

使用 [[顯示診斷](../management/diagnostics.md#show-diagnostics)] 命令來監視叢集中的資料分割進度或狀態。

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

輸出包含：

  * `MinPartitioningPercentageInSingleTable`：在叢集中具有資料分割原則的所有資料表上，分割資料的最小百分比。
    * 如果此百分比持續維持在90% 以下，則請評估叢集的分割[容量](partitioningpolicy.md#capacity)。
  * `TableWithMinPartitioningPercentage`：資料表的完整名稱，如上所示的分割百分比。

使用[. show 命令](commands.md)來監視分割命令及其資源使用率。 例如：

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

#### <a name="capacity"></a>Capacity

* 資料分割程式會導致建立更多的範圍。 叢集可能會逐漸增加其[範圍合併容量](../management/capacitypolicy.md#extents-merge-capacity)，因此[合併範圍](../management/extents-overview.md)的程式可以保持運作。
* 如果有較高的內嵌輸送量，或已定義資料分割原則的大量資料表數目，則叢集可能會逐漸增加其[範圍分割區容量](../management/capacitypolicy.md#extents-partition-capacity)，讓資料[分割範圍的進程](#the-data-partitioning-process)可以趕上。
* 為了避免耗用太多資源，這些動態增加的上限為。 如果完全使用，您可能需要逐漸且線性地增加超過上限的範圍。
  * 如果增加容量會導致使用叢集資源的大幅增加，您可以[up](../../manage-cluster-vertical-scaling.md) / [out](../../manage-cluster-horizontal-scaling.md)手動或藉由啟用自動調整來相應放大叢集。

### <a name="outliers-in-partitioned-columns"></a>分割資料行中的極端值

* 如果雜湊分割區索引鍵所包含的值比其他專案更普遍，例如，空字串或泛型值（例如 `null` 或 `N/A` ），或是代表在 `tenant_id` 資料集內較普遍的實體（例如），可能會導致跨叢集節點的資料不平衡分佈，並降低查詢效能。
* 如果統一範圍的 datetime 資料分割索引鍵的值足以達資料行中大多數值的「遠」百分比，例如，過去或未來的日期時間值，則可能會增加資料分割程式的額外負荷，並導致叢集需要追蹤的許多小範圍。

在這兩種情況下，您都應該「修正」資料，或在內嵌期間篩選掉資料中任何不相關的記錄，以降低叢集上資料分割的額外負荷。 例如，使用[更新原則](updatepolicy.md)。

## <a name="next-steps"></a>後續步驟

使用[分割原則控制命令](../management/partitioning-policy.md)來管理資料表的資料分割原則。
