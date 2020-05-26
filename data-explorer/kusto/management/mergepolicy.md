---
title: 範圍合併原則-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的範圍合併原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: cfb75520a5fa173757395628948c8630a258f935
ms.sourcegitcommit: 4e46b497d518884693a142f4ae21ea497db81861
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83824895"
---
# <a name="extents-merge-policy"></a>範圍合併原則
合併原則會定義是否應該合併 Kusto 叢集中的[範圍（資料分區）](../management/extents-overview.md) 。

合併作業有2種類別： `Merge` （重建索引）和 `Rebuild` （這會完全重新內嵌資料）。

這兩種作業種類都會產生一個取代來源範圍的單一範圍。

根據預設，會建議重建作業，而且只有在有任何剩餘的範圍未符合要重建的準則時，才會嘗試進行合併。  

*注意：*
- 標記使用*不同* `drop-by` 標記的範圍會導致這類範圍不會合並在一起，即使已設定合併原則（請參閱[範圍標記](../management/extents-overview.md#extent-tagging)）。
- 標記的聯集長度超過1百萬個字元的範圍，將不會合並在一起。
- 資料庫的/資料表的[分區化原則](./shardingpolicy.md)也會影響範圍合併在一起的方式。

合併原則包含下列屬性：

- **RowCountUpperBoundForMerge**：
    - 預設為 0。
    - 合併範圍允許的最大資料列計數。
    - 適用于合併作業，而不是重建。  
- **MaxExtentsToMerge**：
    - 預設為100。
    - 要在單一作業中合併的允許範圍數目上限。
    - 適用于合併作業。
- **LoopPeriod**：
    - 預設為01:00:00 （1小時）。
    - 在合併/重建作業的第2個連續反復專案之間等候的時間上限（由資料管理服務執行）。
    - 同時適用于合併和重建作業。
- **AllowRebuild**：
    - 預設為 ' true '。
    - 定義是否 `Rebuild` 啟用作業（在這種情況下，它們優先于 `Merge` 作業）。
- **AllowMerge**：
    - 預設為 ' true '。
    - 定義是否 `Merge` 啟用作業（在這種情況下，它們比作業的偏好更低 `Rebuild` ）。
- **MaxRangeInHours**：
    - 預設值為8。
    - 任何2個不同範圍的建立時間所允許的最大差異（以小時為單位），使其仍然可以合併。
    - 時間戳記是在範圍內建立的，而且不會與範圍中包含的實際資料相關。
    - 同時適用于合併和重建作業。
    - 最佳做法是將此值與資料庫/資料表的[保留原則](./retentionpolicy.md) *SoftDeletePeriod*或快取[原則](./cachepolicy.md)的*DataHotSpan* （兩者的較低者）相互關聯，使其介於後者的2-3% 之間。

**`MaxRangeInHours`典型**
|最小值（SoftDeletePeriod （保留原則），DataHotSpan （快取原則））|最大範圍（以小時為單位）（合併原則）
|---|---
|7天（168小時）| 4
|14天（336小時）| 8
|30天（720小時）| 18
|60天（1440小時）| 36
|90天（2160小時）| 60
|180天（4320小時）| 120
|365天（8760小時）| 250

> [!WARNING]
> 在改變範圍合併原則之前，請洽詢 Azure 資料總管小組。

建立資料庫時，會使用預設的合併原則來設定它（此原則具有上述預設值），這預設會由資料庫中建立的所有資料表繼承（除非在資料表層級明確覆寫其原則）。

您可以在[這裡](../management/merge-policy.md)找到允許管理資料庫/資料表之合併原則的控制命令。
