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
ms.openlocfilehash: 830db0da43da241ffb77ff05f15c0a5f62eb1725
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967565"
---
# <a name="merge-policy"></a>合併原則

合併原則會定義是否應該合併 Kusto 叢集中的[範圍（資料分區）](../management/extents-overview.md) 。

有兩種類型的合併作業： `Merge` 重建索引，以及 `Rebuild` 完全 reingests 資料的。

這兩種作業類型都會產生一個取代來源範圍的範圍。

預設會建議重建作業。 如果有超出要重建之準則的範圍，就會嘗試將它們合併。  

> [!NOTE]
> * 標記使用*不同* `drop-by` 標記的範圍會導致這類範圍不會合並，即使已設定合併原則也一樣。 如需詳細資訊，請參閱[範圍標記](../management/extents-overview.md#extent-tagging)。
> * 標記的聯集長度超過1百萬個字元的範圍將不會合並。
> * 資料庫或資料表的[分區化原則](./shardingpolicy.md)也會影響範圍合併的方式。

## <a name="merge-policy-properties"></a>合併原則屬性

合併原則包含下列屬性：

* **RowCountUpperBoundForMerge**：
    * 預設：
      * 0（無限制），適用于2020年6月之前設定的原則。
      * 16000000，適用于從6月2020開始設定的原則。
    * 合併範圍允許的最大資料列計數。
    * 適用于合併作業，而不是重建。  
* **OriginalSizeMBUpperBoundForMerge**：
    * 預設為0（無限制）。
    * 合併範圍的允許原始大小上限（以 Mb 為單位）。
    * 適用于合併作業，而不是重建。  
* **MaxExtentsToMerge**：
    * 預設為100。
    * 要在單一作業中合併的允許範圍數目上限。
    * 適用于合併作業。
* **LoopPeriod**：
    * 預設為01:00:00 （1小時）。
    * 資料管理服務開始合併或重建作業的兩個連續反復專案之間的等候時間上限。
    * 同時適用于合併和重建作業。
* **AllowRebuild**：
    * 預設為 ' true '。
    * 定義是否 `Rebuild` 啟用作業（在這種情況下，它們是慣用的 `Merge` 作業）。
* **AllowMerge**：
    * 預設為 ' true '。
    * 定義是否 `Merge` 啟用作業，在這種情況下，它們比作業的偏好更低 `Rebuild` 。
* **MaxRangeInHours**：
    * 預設值為8。
    * 允許的差異（以小時為單位），介於兩個不同範圍的建立時間之間，使其仍然可以合併。
    * 時間戳記的範圍是建立的，而且不會與範圍中包含的實際資料相關。
    * 同時適用于合併和重建作業。
    * 這個值應該根據有效的[保留原則](./retentionpolicy.md) *SoftDeletePeriod*或快取[原則](./cachepolicy.md) *DataHotSpan*值來設定。 採用較低的*SoftDeletePeriod*和*DataHotSpan*值。 將*MaxRangeInHours*值設定為介於2-3% 之間。 請參閱[範例](#maxrangeinhours-examples)。

## <a name="maxrangeinhours-examples"></a>MaxRangeInHours 範例

|最小值（SoftDeletePeriod （保留原則），DataHotSpan （快取原則））|最大範圍（以小時為單位）（合併原則）|
|--------------------------------------------------------------------|---------------------------------|
|7天（168小時）                                                  | 4                               |
|14天（336小時）                                                 | 8                               |
|30天（720小時）                                                 | 18                              |
|60天（1440小時）                                               | 36                              |
|90天（2160小時）                                               | 60                              |
|180天（4320小時）                                              | 120                             |
|365天（8760小時）                                              | 250                             |

> [!WARNING]
> 在改變範圍合併原則之前，請洽詢 Azure 資料總管小組。

建立資料庫時，會使用上述的預設合併原則值進行設定。 除非在資料表層級明確覆寫其原則，否則在資料庫中建立的所有資料表預設會繼承原則。

如需詳細資訊，請參閱[控制命令，可讓您管理資料庫或資料表的合併原則](../management/merge-policy.md)。
