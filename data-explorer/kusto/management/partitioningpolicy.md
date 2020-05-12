---
title: 資料分割原則（預覽）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料分割原則（預覽）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 564ce0677f3d280fed27c0b6ce1328cb35188c4f
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225882"
---
# <a name="data-partitioning-policy-preview"></a>資料分割原則（預覽）

分割原則會針對特定資料表定義是否應該分割[範圍（資料分區）](../management/extents-overview.md) 。

> [!NOTE]
> 資料分割功能目前為*預覽*狀態。

原則的主要目的是要改善已知會縮小至分割區資料行中的一小部分值的查詢效能，以及（或）高基數位符串資料行上的匯總/聯結。 次要的可能優點是資料的壓縮較佳。

> [!WARNING]
> 雖然未針對可定義原則的資料表數量設定硬式編碼限制，但每個額外的資料表都會增加在叢集節點上執行的背景資料分割進程的負擔，而且可能需要叢集的其他資源-請參閱[容量](#capacity)。

## <a name="partition-keys"></a>資料分割索引鍵

支援下列幾種資料分割索引鍵：

|種類                                                   |資料行類型 |磁碟分割內容                    |資料分割值                                        |
|-------------------------------------------------------|------------|----------------------------------------|-------------------------------------------------------|
|[雜湊](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[統一範圍](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>雜湊分割區索引鍵

`string`當*大部分*的查詢 `==` `in()` 在 `string` *大型維度*（1千萬個或更高的基數）的特定類型資料行上使用相等篩選（，）和（或）匯總/聯結（例如 `application_ID` 、 `tenant_ID` 或） `user_ID` 時，將雜湊分割區索引鍵套用在資料表的類型資料行上是適當的。

* 雜湊模數函數可用來分割資料。
* 屬於相同資料分割的所有*同質*（資料分割）範圍都會指派給相同的資料節點。
* *同質*（已分割）範圍中的資料是依雜湊資料分割索引鍵來排序。
  * 不需要在資料[列順序原則](roworderpolicy.md)中包含分割區索引鍵（如果資料表上有定義）。
* 使用[隨機播放策略](../query/shufflequery.md)的查詢，以及 `shuffle key` 資料表的雜湊分割區索引鍵所使用的，其 `join` `summarize` 預期會 `make-series` 執行得更好，因為在叢集節點之間移動所需的資料量會大幅降低。

#### <a name="partition-properties"></a>磁碟分割內容

* `Function`這是要使用的雜湊模數函數名稱。
  * 支援的值： `XxHash64` 。
* `MaxPartitionCount`這是每個時間週期內所要建立的分割區數目上限（雜湊模數函數的模數引數）。
  * 支援的為範圍中的值 `(1,1024]` 。
    * 此值應為：
      * 大於叢集中的節點數目
      * 小於資料行的基數。
    * 值愈高，叢集節點上資料分割程式的額外負荷就會愈大，而每個時間週期的範圍數目也會越高。
    * 建議以開頭的值為 `256` 。
      * 如有需要，您可以根據前述考慮來調整（通常為向上），並根據測量查詢效能的優點與分割資料的額外負荷（在內嵌之後）。
* `Seed`這是用來隨機化雜湊值的值。
  * 此值應為正整數。
  * 建議的（如果未指定，則為預設值）為 `1` 。

#### <a name="example"></a>範例

下列是在名為的類型資料行上的雜湊資料分割索引鍵 `string` `tenant_id` 。

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

`datetime`當不*太可能*根據這個資料行來排序資料表中的資料內嵌時，將統一範圍的 datetime 資料分割索引鍵套用到資料表中的類型資料行上是適當的。 在這種情況下，在範圍之間重新隨機播放資料可能會很有説明，因為每個範圍最後都會包含有限時間範圍內的記錄，因此在查詢時資料行上的篩選會 `datetime` 更有效率。

* 使用的資料分割函數是[bin_at （）](../query/binatfunction.md) ，無法自訂。

#### <a name="partition-properties"></a>磁碟分割內容

* `RangeSize`這是一個純 `timespan` 量常數，表示每個 datetime 資料分割的大小。
  * 建議以開頭的值為 `1.00:00:00` （一天）。
  * 設定的值明顯比*不*建議更短，因為它可能會導致具有大量小型範圍的資料表無法合併在一起。
* `Reference`這是類型的純量 `datetime` 常數，表示根據哪一個日期時間資料分割對齊的固定時間點。
  * 建議以開頭的值為 `1970-01-01 00:00:00` 。
  * 如果日期時間分割區索引鍵有值的記錄 `null` ，其資料分割值會設為的值 `Reference` 。

#### <a name="example"></a>範例

以下是在名為的具類型資料行上的統一 datetime 範圍資料分割索引鍵 `datetime``timestamp`

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

根據預設，資料表的資料分割原則是 `null` ，在這種情況下，資料表中的資料將不會進行分割。

資料分割原則具有下列主要屬性：

* **PartitionKeys**：
  * 分割區索引[鍵](#partition-keys)的集合，定義如何分割資料表中的資料。
  * 資料表可以有多個分割區索引 `2` 鍵，並具有下列3個選項的其中一個：
    * 1個[雜湊資料分割索引鍵](#hash-partition-key)。
    * 1[統一範圍 datetime 資料分割索引鍵](#uniform-range-datetime-partition-key)。
    * 1個[雜湊資料分割索引鍵](#hash-partition-key)和1個[統一範圍的 datetime 資料分割索引鍵](#uniform-range-datetime-partition-key)。
  * 每個分割區索引鍵都有下列屬性：
    * `ColumnName`： `string ` -資料行的名稱，將會根據該資料分割資料。
    * `Kind`： `string` -要套用的資料分割類型（ `Hash` 或 `UniformRange` ）。
    * `Properties`： `property bag` -根據完成的資料分割定義參數。

* **EffectiveDateTime**：
  * 原則生效的 UTC 日期時間。
  * 這個屬性是*選擇性*的，如果未指定，原則會在套用原則之後，于資料內嵌上生效。
  * 所有系結至的非同質（非資料分割）範圍，由於保留期（也就是在資料表的有效虛刪除期間90% 之前）都會被捨棄，因此資料分割程式會略過。

### <a name="example"></a>範例

以下是具有兩個分割區索引鍵的資料分割原則物件：
1. 具有類型之資料行的雜湊資料分割索引鍵 `string` `tenant_id` 。
    - 它會使用 `XxHash64` 雜湊函式，其為 `MaxPartitionCount` 256，而預設值為 `Seed` `1` 。
2. 具有類型之資料行的統一 datetime 範圍資料分割索引鍵 `datetime``timestamp`
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

下列屬性可以定義為原則的一部分，但是*選擇性*的，不建議變更。

* **MinRowCountPerOperation**：
  * 單一資料分割作業之來源範圍的資料列計數總和的最小目標。
  * 這個屬性是*選擇性*的，其預設值為 `0` 。

* **MaxRowCountPerOperation**：
  * 單一資料分割作業之來源範圍的資料列計數總和的最大目標。
  * 這個屬性是*選擇性*的，其預設值為 `0` （在此情況下，5000000記錄的預設目標會生效）。

## <a name="notes"></a>附註

### <a name="the-data-partitioning-process"></a>資料分割進程

* 資料分割會在叢集中做為內嵌後背景進程執行。
  * 連續內嵌到的資料表預期一律會有資料的「結尾」，但尚未分割（*非同質*範圍）。
* 不論原則中的屬性值為何，資料分割都只會在經常性存取範圍上執行 `EffectiveDateTime` 。
  * 如果需要分割冷範圍，您必須暫時相應地調整快取[原則](cachepolicy.md)。

#### <a name="monitoring"></a>監視

* 您可以使用[. show diagnostics](../management/diagnostics.md#show-diagnostics)命令，監視叢集中的資料分割進度/狀態：

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable,
          TableWithMinPartitioningPercentage
```

輸出包含：

  * `MinPartitioningPercentageInSingleTable`：在叢集中具有資料分割原則的所有資料表上，分割資料的最小百分比。
      * 如果此百分比持續維持在90% 以下，請評估叢集的分割容量（[如下](partitioningpolicy.md#capacity)所示）。
  * `TableWithMinPartitioningPercentage`：資料表的完整名稱，如上所示的分割百分比。

#### <a name="capacity"></a>Capacity

* 資料分割程式會導致建立更多的範圍。 叢集可能會逐漸增加其[範圍合併容量](../management/capacitypolicy.md#extents-merge-capacity)，因此[合併範圍](../management/extents-overview.md)的程式可以保持運作。
* 如果有大量的內建輸送量，以及（或）具有已定義資料分割原則的大量資料表，叢集可能會逐漸增加其[範圍分割區容量](../management/capacitypolicy.md#extents-partition-capacity)，讓資料[分割範圍的進程](#the-data-partitioning-process)能夠趕上。
* 為了避免耗用太多資源，這些動態增加的上限為。 如果超量而時，您可能需要（逐漸和線性）將它們增加超過上限。
  * 如果增加容量會導致使用叢集資源的大幅增加，您可以[up](../../manage-cluster-vertical-scaling.md) / [out](../../manage-cluster-horizontal-scaling.md)手動或藉由啟用自動調整來相應放大叢集。

### <a name="outliers-in-partitioned-columns"></a>分割資料行中的極端值

* 如果雜湊分割區索引鍵的值夠大，而無法正確填入（例如，它們是空的或具有一般值），這可能會導致在叢集節點之間擁有非平衡的資料散發。
* 如果統一範圍的 datetime 資料分割索引鍵有夠多百分比的值，這些值是從資料行中大部分的值「遠」（例如，過去或未來的日期時間值）。

在這兩種情況下，您都應該「修正」資料，或在內嵌時（例如，使用[更新原則](updatepolicy.md)）篩選掉資料中任何不相關的記錄，以降低叢集上資料分割的額外負荷。

## <a name="next-steps"></a>後續步驟

使用[分割原則控制命令](../management/partitioning-policy.md)來管理資料表的資料分割原則。
