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
ms.openlocfilehash: c3f7212b062adaae1bd56399753270653204ad22
ms.sourcegitcommit: 53a727fceaa89e6022bc593a4aae70f1e0232f49
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652106"
---
# <a name="data-partitioning-policy"></a>資料分割原則

分割原則會針對特定資料表，定義是否要分割 [ (資料分區) 的範圍 ](../management/extents-overview.md) 和方式。

原則的主要目的是要藉由篩選資料分割資料行，或在高基數位符串資料行上進行匯總/聯結，來改善已知可縮小資料集範圍的查詢效能。 原則也可能會導致資料壓縮更好。

> [!CAUTION]
> 在可以定義原則的資料表數目上，沒有任何硬式編碼的限制設定。 不過，每個額外的資料表都會在叢集節點上執行的背景資料分割進程增加額外負荷。 它可能會導致使用更多的叢集資源。 如需詳細資訊，請參閱 [監視](#monitoring) 和 [容量](#capacity)。

## <a name="partition-keys"></a>資料分割索引鍵

支援下列類型的分割區索引鍵。

|種類                                                   |資料行類型 |磁碟分割內容                    |分割區值                                        |
|-------------------------------------------------------|------------|----------------------------------------|----------------------|
|[雜湊](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[統一範圍](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>雜湊分割索引鍵

> [!NOTE]
> `string`只有在下列情況下，才會在資料表中的類型資料行上套用雜湊分割索引鍵：
> * 如果大部分的查詢都使用相等篩選 (`==` ， `in()`) 。
> * 大部分的查詢會針對 `string` 1 千萬個或更高) 的 *大型維度* (基數的特定類型資料行進行匯總/聯結 `application_ID` ，例如，、 `tenant_ID` 或 `user_ID` 。

* 雜湊模數函數用來分割資料。
* 同質 (資料分割) 範圍中的資料會依照雜湊分割索引鍵來排序。
  * 如果資料表上有定義雜湊分割索引鍵，則您不需要將它包含在資料 [列順序原則](roworderpolicy.md)中。
* 使用 [隨機執行策略](../query/shufflequery.md)的查詢， `shuffle key` `join` `summarize` 或 `make-series` 資料表的雜湊分割索引鍵所使用的查詢，會因為在叢集節點之間移動所需的資料量大幅降低而變得更好。

#### <a name="partition-properties"></a>磁碟分割內容

* `Function` 這是要使用的雜湊模數函數名稱。
  * 支援的值： `XxHash64` 。
* `MaxPartitionCount` 這是要建立的分割區數目上限， (雜湊模數函數的模數引數) 每一段時間。
  * 支援的值位於範圍內 `(1,2048]` 。
    * 值應為：
      * 大於叢集中節點數目的5倍。
      * 小於資料行的基數。
    * 值愈高，在叢集節點上，資料分割進程的額外負荷就愈大，而且每個時間週期的範圍數目愈高。
    * 針對具有少於50個節點的叢集，我們建議您從的值開始 `256` 。
      * 根據上述考慮，視需要調整值 (例如，叢集中的節點數目會成長) ，或根據查詢效能的優點，以及在內嵌資料分割資料的額外負荷。
* `Seed` 這是用來隨機化雜湊值的值。
  * 值應為正整數。
  * 建議值為 `1` ，如果未指定，則為預設值。
* `PartitionAssignmentMode` 這是用來將磁碟分割指派給叢集中節點的模式。
  * 支援的模式：
    * `Default`：屬於相同資料分割的所有同質 (分割) 範圍都會指派給相同的節點。
    * `Uniform`：會忽略範圍的資料分割值，而且會將範圍統一指派給叢集的節點。
  * 如果查詢未聯結或匯總雜湊資料分割索引鍵使用 `Uniform` 。 否則，使用 `Default`。

#### <a name="example"></a>範例

名為之類型資料行上的雜湊分割索引鍵 `string` `tenant_id` 。
它會使用 `XxHash64` 雜湊函式， `MaxPartitionCount` 並搭配的 `256` 和預設 `Seed` 值 `1` 。

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1,
    "PartitionAssignmentMode": "Default"
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>統一範圍 datetime 分割區索引鍵

> [!NOTE] 
> `datetime`當內嵌到資料表中的資料不太可能根據此資料行排序時，只會在資料表的類型資料行上套用統一範圍 datetime 分割區索引鍵。

在這種情況下，在範圍之間重新資料可能會很有説明，如此一來，每個範圍最後會包含限制時間範圍內的記錄。 這會導致該資料行上的篩選準則 `datetime` 在查詢時更有效率。

使用的資料分割函數是 [bin_at ( # B1 ](../query/binatfunction.md) ，無法自訂。

#### <a name="partition-properties"></a>磁碟分割內容

* `RangeSize` 這是純 `timespan` 量常數，表示每個 datetime 磁碟分割的大小。
  建議您：
  * 從 `1.00:00:00` (一天) 的值開始。
  * 請勿設定較短的值，因為它可能會導致資料表有大量無法合併的小範圍。
* `Reference` 這是純 `datetime` 量常數，會根據對齊的日期時間分割來指出固定的時間點。
  * 我們建議您從開始 `1970-01-01 00:00:00` 。
  * 如果有 datetime 分割區索引鍵具有值的記錄 `null` ，其資料分割值會設定為的值 `Reference` 。

#### <a name="example"></a>範例

程式碼片段會在名為的具類型資料行上顯示統一的 datetime 範圍資料分割索引鍵 `datetime` `timestamp` 。
它會使用 `datetime(1970-01-01)` 做為其參考點， `1d` 每個分割區的大小都是。

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

依預設，資料表的資料分割原則是 `null` ，在此情況下，資料表中的資料不會進行資料分割。

資料分割原則具有下列主要屬性：

* **PartitionKeys**：
  * 資料分割索引 [鍵](#partition-keys) 的集合，定義如何分割資料表中的資料。
  * 資料表可具有最多資料 `2` 分割索引鍵，並具有下列其中一個選項。
    * 一個 [雜湊分割索引鍵](#hash-partition-key)。
    * 一個 [統一範圍的 datetime 分割區索引鍵](#uniform-range-datetime-partition-key)。
    * 一個 [雜湊資料分割索引鍵](#hash-partition-key) 和一個 [統一範圍的 datetime 分割區索引鍵](#uniform-range-datetime-partition-key)。
  * 每個分割區索引鍵都具有下列屬性：
    * `ColumnName`： `string ` 根據資料分割的資料行名稱。
    * `Kind`： `string` 要套用 (或) 的資料分割 `Hash` 種類 `UniformRange` 。
    * `Properties`： `property bag` -根據完成的資料分割定義參數。

* **EffectiveDateTime**：
  * 原則生效的 UTC 日期時間。
  * 這是選用屬性。 如果未指定，則原則會在套用原則之後對資料內嵌生效。
  * 資料分割進程會忽略任何可能因為保留而卸載的非同質 (非資料分割) 範圍，因為它們的建立時間在資料表有效的虛刪除期間的90% 之前。

### <a name="example"></a>範例

具有兩個分割區索引鍵的資料分割原則物件。
1. 名為之類型資料行上的雜湊分割索引鍵 `string` `tenant_id` 。
    - 它使用雜湊函式，其為 `XxHash64` `MaxPartitionCount` 256，預設值為 `Seed` `1` 。
1. 名為之類型資料行上的統一 datetime 範圍資料分割索引鍵 `datetime` `timestamp` 。
    - 它會使用 `datetime(1970-01-01)` 做為其參考點， `1d` 每個分割區的大小都是。

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1,
        "PartitionAssignmentMode": "Default"
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

下列屬性可以定義為原則的一部分，但是是選擇性的，我們建議您不要變更它們。

* **MinRowCountPerOperation**：
  * 單一資料分割作業的來源範圍之資料列計數總和的最小目標。
  * 這是選用屬性。 其預設值為 `0`。

* **MaxRowCountPerOperation**：
  * 單一資料分割作業的來源範圍之資料列計數總和的最大目標。
  * 這是選用屬性。 其預設值為 `0` ，預設目標為5000000記錄。
    * 如果您看到資料分割作業耗用極大量的記憶體或 CPU （每項作業），則可以設定低於5M 的值。 如需詳細資訊，請參閱 [監視](#monitoring)。

## <a name="notes"></a>注意

### <a name="the-data-partitioning-process"></a>資料分割進程

* 資料分割會在叢集中以內嵌的背景進程的形式執行。
  * 持續內嵌至的資料表應該一律具有尚未分割 (非同質範圍) 的資料的「結尾」。
* 無論原則中的屬性值為何，資料分割都只會在作用區上執行 `EffectiveDateTime` 。
  * 如果需要分割冷區，您將需要暫時調整快取 [原則](cachepolicy.md)。

#### <a name="monitoring"></a>監視

使用 [ [顯示診斷](../management/diagnostics.md#show-diagnostics) ] 命令來監視叢集中的資料分割進度或狀態。

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

輸出包含：

  * `MinPartitioningPercentageInSingleTable`：在叢集中具有資料分割原則的所有資料表之分割資料的最短百分比。
    * 如果此百分比持續維持在90% 以下，則請評估叢集的資料分割 [容量](partitioningpolicy.md#capacity)。
  * `TableWithMinPartitioningPercentage`：資料表的完整名稱，其分割區百分比顯示于上方。

使用 [. 顯示命令](commands.md) 來監視分割命令及其資源使用率。 例如：

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

#### <a name="capacity"></a>Capacity

* 資料分割進程會導致建立更多範圍。 叢集可能會逐漸增加其 [範圍合併容量](../management/capacitypolicy.md#extents-merge-capacity)，讓 [合併範圍](../management/extents-overview.md) 的程式可以保持運作。
* 如果有高的內嵌輸送量，或有定義分割原則的大量資料表，則叢集可能會逐漸增加分割區的 [範圍](../management/capacitypolicy.md#extents-partition-capacity)，以便讓資料 [分割範圍的進程](#the-data-partitioning-process) 保持可用。
* 為了避免耗用太多資源，這些動態增加的限制為上限。 如果已完全使用，您可能需要以逐漸且線性的方式將它們增加到超過上限。
  * 如果增加容量會大幅增加使用叢集的資源，您可以[up](../../manage-cluster-vertical-scaling.md) / [out](../../manage-cluster-horizontal-scaling.md)手動或藉由啟用自動調整來調整叢集。

### <a name="outliers-in-partitioned-columns"></a>分割資料行中的極端值

* 如果雜湊資料分割索引鍵所包含的值比其他值更普遍（例如，空字串或泛型值 (例如 `null` 或 `N/A`) ），或代表資料集內更普遍的實體 (例如 `tenant_id`) ，則可能會對叢集節點上的資料不平衡分佈造成影響，並降低查詢效能。
* 如果統一範圍的日期時間分割區索引鍵具有夠大的百分比值，而這些值與資料行中大部分的值「遠」相同（例如，過去或未來的日期時間值），則可能會增加資料分割進程的額外負荷，並導致叢集需要追蹤的許多小型範圍。

在這兩種情況下，您應該「修正」資料，或在內嵌時間之前或之後篩選出資料中任何不相關的記錄，以減少叢集上資料分割的額外負荷。 例如，使用 [更新原則](updatepolicy.md)。

## <a name="next-steps"></a>後續步驟

使用資料 [分割原則控制命令](../management/partitioning-policy.md) 來管理資料表的資料分割原則。
