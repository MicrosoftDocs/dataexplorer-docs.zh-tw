---
title: 範圍合併原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的範圍合併原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 87980046e6f0ebbbdd17a9037aa1206779d2a61e
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057031"
---
# <a name="merge-policy"></a>合併原則

合併原則會定義是否要合併 Kusto 叢集中 [ (資料分區) 的範圍 ](../management/extents-overview.md) 和方式。

有兩種類型的合併作業： `Merge` 、會重建索引，以及 `Rebuild` 完全 reingests 資料的類型。

這兩種作業類型都會產生一個取代來源範圍的範圍。

依預設，會優先使用重建作業。 如果有不符合要重建之準則的範圍，則會嘗試將它們合併。  

> [!NOTE]
> * 使用 *不同* `drop-by` 標記來標記範圍將會導致這類範圍無法合併，即使已設定合併原則也一樣。 如需詳細資訊，請參閱 [範圍標記](../management/extents-overview.md#extent-tagging)。
> * 不會合並標記的聯集長度超過1M 個字元的範圍。
> * 資料庫或資料表的 [分區化原則](./shardingpolicy.md) 也會對如何合併範圍有一些影響。

## <a name="merge-policy-properties"></a>合併原則屬性

合併原則包含下列屬性：

* **RowCountUpperBoundForMerge**：
    * 預設：
      * 0 (在2020年6月之前設定之原則的無限制) 。
      * 16000000，適用于從2020年6月開始設定的原則。
    * 合併範圍允許的最大資料列計數。
    * 適用于合併作業，不會重建。  
* **OriginalSizeMBUpperBoundForMerge**：
    * 預設值為 0 (無限制的) 。
    * 允許的原始大小上限 (Mb) 的合併範圍。
    * 適用于合併作業，不會重建。  
* **MaxExtentsToMerge**：
    * 預設為100。
    * 要在單一作業中合併的最大允許範圍數目。
    * 適用于合併作業。
* **LoopPeriod**：
    * 預設為 01:00:00 (1 小時) 。
    * 資料管理服務開始合併或重建作業的兩個連續反覆運算之間，等候的最長時間。
    * 適用于合併和重建作業。
* **AllowRebuild**：
    * 預設值為 ' true '。
    * 定義是否 `Rebuild` 啟用作業 (在這種情況下，它們優先于 `Merge` 作業) 。
* **AllowMerge**：
    * 預設值為 ' true '。
    * 定義是否要 `Merge` 啟用作業，在這種情況下，它們的偏好比 `Rebuild` 作業低。
* **MaxRangeInHours**：
    * 預設值為8。
        * 除非已在具體化視圖的有效[保留原則](retentionpolicy.md)中停用復原功能，否則[具體化視圖](materialized-views/materialized-view-overview.md)中的預設值為14天。
    * 任何兩個不同範圍的建立時間之間允許的最大差異（以小時為單位），以便仍可合併。
    * 時間戳記的範圍是建立的，而且不會與範圍內包含的實際資料相關。
    * 適用于合併和重建作業。
    * 此值應根據有效的 [保留原則](./retentionpolicy.md) *SoftDeletePeriod*或快取 [原則](./cachepolicy.md) *DataHotSpan* 值來設定。 採用 *SoftDeletePeriod* 和 *DataHotSpan*的較低值。 將 *MaxRangeInHours* 值設定為介於2-3% 之間的值。 請參閱 [範例](#maxrangeinhours-examples) 。

## <a name="maxrangeinhours-examples"></a>MaxRangeInHours 範例

|最小 (SoftDeletePeriod (保留原則) ，DataHotSpan (快取原則) # A5|合併原則)  (的最大範圍（小時）|
|--------------------------------------------------------------------|---------------------------------|
|7天 (168 小時)                                                   | 4                               |
|14天 (336 小時)                                                  | 8                               |
|30天 (720 小時)                                                  | 18                              |
|60天 (1440 小時)                                                | 36                              |
|90天 (2160 小時)                                                | 60                              |
|180天 (4320 小時)                                               | 120                             |
|365天 (8760 小時)                                               | 250                             |

> [!WARNING]
> 在變更範圍合併原則之前，請洽詢 Azure 資料總管團隊。

建立資料庫時，它會以上述的預設合併原則值進行設定。 除非在資料表層級明確覆寫原則，否則此原則預設會由資料庫中建立的所有資料表繼承。

如需詳細資訊，請參閱 [控制命令，讓您管理資料庫或資料表的合併原則](../management/merge-policy.md)。
