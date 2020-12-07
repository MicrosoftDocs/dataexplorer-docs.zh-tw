---
title: 資料分割原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的資料分割原則，以及如何使用它來改善查詢效能。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: 30929e63c39be10d066815333ba6b277c0aeb5c9
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321279"
---
# <a name="partitioning-policy"></a>資料分割原則

分割原則會定義是否應該針對特定資料表分割 [ (資料分區) 的範圍 ](../management/extents-overview.md) 。

原則的主要目的是要改善縮小資料集的查詢效能。例如，針對資料分割資料行進行篩選的查詢、使用匯總，或在高基數位符串資料行上聯結。 原則也可能會導致資料壓縮更好。

> [!CAUTION]
> 針對已定義分割原則的資料表數目，沒有設定任何硬式編碼的限制。 不過，每個額外的資料表都會在叢集節點上執行的背景資料分割進程增加額外負荷。 加入資料表可能會導致使用更多的叢集資源。 如需詳細資訊，請參閱 [監視](#monitor-partitioning) 和 [容量](#partition-capacity)。

## <a name="partition-keys"></a>資料分割索引鍵

支援下列類型的分割區索引鍵。

|類型                                                   |資料行類型 |磁碟分割內容                                               |分割區值                                        |
|-------------------------------------------------------|------------|-------------------------------------------------------------------|-------------------------------------------------------|
|[雜湊](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed`, `PartitionAssignmentMode` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[統一範圍](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`, `OverrideCreationTime`                   | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>雜湊分割索引鍵

> [!NOTE]
> `string`只有在下列情況下，才會在資料表中的類型資料行上套用雜湊分割索引鍵：
> * 如果大部分的查詢都使用相等篩選 (`==` ， `in()`) 。
> * 大部分的查詢會針對 `string` 1 千萬個或更高) 的 *大型維度* (基數的特定類型資料行進行匯總/聯結 `application_ID` ，例如，、 `tenant_ID` 或 `user_ID` 。

* 雜湊模數函數用來分割資料。
* 同質 (資料分割) 範圍中的資料會依照雜湊分割索引鍵來排序。
  * 如果資料表上有定義雜湊分割索引鍵，則您不需要將它包含在資料 [列順序原則](roworderpolicy.md)中。
* 使用 [隨機執行策略](../query/shufflequery.md)的查詢， `shuffle key` `join` `summarize` 或 `make-series` 資料表的雜湊分割索引鍵所使用的查詢，會因為跨叢集節點移動所需的資料量減少而變得更好。

#### <a name="partition-properties"></a>磁碟分割內容

|屬性 | 描述 | 支援的值 (s) | 建議值 |
|---|---|---|---|
| `Function` | 要使用的雜湊模數函數名稱。| `XxHash64` | |
| `MaxPartitionCount` | 要建立的分割區數目上限 (雜湊模數函數的模數引數) 每一段時間。 | 範圍中的 `(1,2048]` 。 <br>  大於叢集中節點數目的五倍，且小於資料行的基數。 |  較高的值會導致叢集節點上的資料分割進程有更高的負荷，而且每一段時間的範圍也會更高。 針對具有少於50個節點的叢集，請從開始 `256` 。 根據這些考慮來調整值，或根據查詢效能的優點，和在內嵌資料分割資料的額外負荷來調整值。
| `Seed` | 用來隨機化雜湊值。 | 正整數。 | `1`，也是預設值。 |
| `PartitionAssignmentMode` | 用來將磁碟分割指派給叢集中節點的模式。 | `Default`：屬於相同資料分割的所有同質 (分割) 範圍都會指派給相同的節點。 <br> `Uniform`：已忽略範圍的資料分割值。 範圍會統一指派給叢集的節點。 | 如果查詢未聯結或匯總雜湊資料分割索引鍵，請使用 `Uniform` 。 否則，使用 `Default`。 |

#### <a name="hash-partition-key-example"></a>雜湊資料分割索引鍵範例

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

在這些情況下，您可以重新範圍之間的資料，讓每個範圍都包含有限時間範圍內的記錄。 此程式會導致資料行上的篩選準則在 `datetime` 查詢時更有效率。

使用的資料分割函數是 [bin_at ( # B1 ](../query/binatfunction.md) ，無法自訂。

#### <a name="partition-properties"></a>磁碟分割內容

|屬性                | 描述                                                                                                                                                     | 建議值                                                                                                                                                                                                                                                            |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `RangeSize`            | `timespan`指出每個 datetime 資料分割大小的純量常數。                                                                                | 從 `1.00:00:00` (一天) 的值開始。 請勿設定較短的值，因為它可能會導致資料表有大量無法合併的小範圍。                                                                                                      |
| `Reference`            | `datetime`表示固定時間點的純量常數，根據對齊的日期時間分割區而定。                                          | 請從 `1970-01-01 00:00:00` 開始。 如果有 datetime 分割區索引鍵具有值的記錄 `null` ，其資料分割值會設定為的值 `Reference` 。                                                                                                      |
| `OverrideCreationTime` | `bool`，指出是否應該以分割區索引鍵的值範圍覆寫結果範圍的最小和最大建立時間。 | 預設值為 `false`。 `true`如果資料未依 (抵達時間的順序內嵌，例如單一來源檔案可能會包含非常遠的) 的日期時間值，以及/或您想要根據日期時間值強制保留/快取，而不是內嵌的時間，則設定為。 |

#### <a name="uniform-range-datetime-partition-example"></a>統一範圍 datetime 分割區範例

程式碼片段會在名為的具類型資料行上顯示統一的 datetime 範圍資料分割索引鍵 `datetime` `timestamp` 。
它會使用 `datetime(1970-01-01)` 做為其參考點， `1d` 每個分割區的大小為，且不會覆寫範圍的建立時間。

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00",
    "OverrideCreationTime": false
  }
}
```

## <a name="the-policy-object"></a>原則物件

依預設，資料表的資料分割原則是 `null` ，在此情況下，資料表中的資料不會進行資料分割。

資料分割原則具有下列主要屬性：

* **PartitionKeys**：
  * 資料分割索引 [鍵](#partition-keys) 的集合，定義如何分割資料表中的資料。
  * 資料表可具有最多資料 `2` 分割索引鍵，並具有下列其中一個選項：
    * 一個 [雜湊分割索引鍵](#hash-partition-key)。
    * 一個 [統一範圍的 datetime 分割區索引鍵](#uniform-range-datetime-partition-key)。
    * 一個 [雜湊資料分割索引鍵](#hash-partition-key) 和一個 [統一範圍的 datetime 分割區索引鍵](#uniform-range-datetime-partition-key)。
  * 每個分割區索引鍵都具有下列屬性：
    * `ColumnName`： `string ` 根據資料分割的資料行名稱。
    * `Kind`： `string` 要套用 (或) 的資料分割 `Hash` 種類 `UniformRange` 。
    * `Properties`： `property bag` -根據完成的資料分割定義參數。

* **EffectiveDateTime**：
  * 原則生效的 UTC 日期時間。
  * 這是選用屬性。 如果未指定，原則將會在套用原則之後對資料內嵌生效。
  * 因為資料分割進程會忽略保留的任何非同質 (非資料分割) 範圍。 系統會忽略範圍，因為它們的建立時間在資料表有效的虛刪除期間的90% 之前。
    > [!NOTE]
    > 您可以設定過去和資料分割已內嵌資料中的日期時間值。 不過，這種作法可能會大幅增加分割進程中使用的資源。 

### <a name="data-partitioning-example"></a>資料分割範例

具有兩個分割區索引鍵的資料分割原則物件。
1. 名為之類型資料行上的雜湊分割索引鍵 `string` `tenant_id` 。
    * 它使用雜湊函式，其為 `XxHash64` `MaxPartitionCount` 256，預設值為 `Seed` `1` 。
1. 名為之類型資料行上的統一 datetime 範圍資料分割索引鍵 `datetime` `timestamp` 。
    * 它會使用 `datetime(1970-01-01)` 做為其參考點， `1d` 每個分割區的大小都是。

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
        "RangeSize": "1.00:00:00",
        "OverrideCreationTime": false
      }
    }
  ]
}
```

## <a name="additional-properties"></a>其他屬性

下列屬性可以定義為原則的一部分。 這些屬性是選擇性的，我們建議您不要變更它們。

|屬性 | 描述 | 建議值 | 預設值 |
|---|---|---|---|
| **MinRowCountPerOperation** |  單一資料分割作業的來源範圍之資料列計數總和的最小目標。 | | `0` |
| **MaxRowCountPerOperation** |  單一資料分割作業的來源範圍之資料列計數總和的最大目標。 | 如果您看到資料分割作業耗用海量儲存體或 CPU 的每個作業，請設定低於5M 的值。 如需詳細資訊，請參閱 [監視](#monitor-partitioning)。 | `0`，預設目標為5000000記錄。 |

## <a name="the-data-partitioning-process"></a>資料分割進程

* 資料分割會在叢集中以內嵌的背景進程的形式執行。
  * 持續內嵌至的資料表應該一律具有尚未分割 (非同質範圍) 的資料的「結尾」。
* 無論原則中的屬性值為何，資料分割都只會在作用區上執行 `EffectiveDateTime` 。
  * 如果需要分割冷區，您需要暫時調整快取 [原則](cachepolicy.md)。

## <a name="monitor-partitioning"></a>監視資料分割

使用命令來監視叢集中的資料 [`.show diagnostics`](../management/diagnostics.md#show-diagnostics) 分割進度或狀態。

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

輸出包含：

  * `MinPartitioningPercentageInSingleTable`：在叢集中具有資料分割原則的所有資料表之分割資料的最短百分比。
    * 如果此百分比持續維持在90% 以下，則請評估叢集的資料分割 [容量](partitioningpolicy.md#partition-capacity)。
  * `TableWithMinPartitioningPercentage`：資料表的完整名稱，其分割區百分比顯示于上方。

用 [`.show commands`](commands.md) 來監視分割命令及其資源使用。 例如：

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

## <a name="partition-capacity"></a>資料分割容量

* 資料分割進程會導致建立更多範圍。 叢集可能會逐漸增加其 [範圍合併容量](../management/capacitypolicy.md#extents-merge-capacity)，讓 [合併範圍](../management/extents-overview.md) 的程式可以保持運作。
* 如果有高的內嵌輸送量，或有定義分割原則的大量資料表，則叢集可能會逐漸增加分割區的 [範圍](../management/capacitypolicy.md#extents-partition-capacity)，以便讓資料 [分割範圍的進程](#the-data-partitioning-process) 保持可用。
* 為了避免耗用太多資源，這些動態增加的限制為上限。 如果已完全使用，您可能需要以逐漸且線性的方式將它們增加到超過上限。
  * 如果增加容量會大幅增加使用叢集的資源，您可以[up](../../manage-cluster-vertical-scaling.md) / [out](../../manage-cluster-horizontal-scaling.md)手動或藉由啟用自動調整來調整叢集。

## <a name="outliers-in-partitioned-columns"></a>分割資料行中的極端值

* 下列情況可能會對叢集節點上的資料不平衡散發造成影響，並降低查詢效能：
    * 如果雜湊資料分割索引鍵所包含的值比其他值更普遍，例如空字串或泛型值 (例如 `null` 或 `N/A`) 。
    * 這些值代表實體 (，例如 `tenant_id` 在資料集中更普遍的) 。
* 如果統一範圍的日期時間分割區索引鍵具有的百分比值，與資料行中大部分的值「遠」相同，則資料分割進程的額外負荷會增加，而且可能會導致叢集需要追蹤的許多小型範圍。 這類情況的其中一個範例是來自于過去或未來的日期時間值。

在這兩種情況下，都可以「修正」資料，或在內嵌期間篩選出資料中的任何無關記錄，以減少叢集上資料分割的額外負荷。 例如，使用 [更新原則](updatepolicy.md)。

## <a name="next-steps"></a>後續步驟

使用資料 [分割原則控制命令](../management/partitioning-policy.md) 來管理資料表的資料分割原則。
