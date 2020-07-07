---
title: 架構設計的最佳做法-Azure 資料總管
description: 本文說明 Azure 資料總管中架構設計的最佳作法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: a16cb4b425e26a5896b4109ad6f906b5925c93a5
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013748"
---
# <a name="best-practices-for-schema-design"></a>架構設計的最佳作法

以下是幾個要遵循的最佳作法。 其可協助讓您的管理命令效果更好，而且對服務資源的影響會較低。

|動作  |用法  |不使用 | 備忘稿 |
|---------|---------|---------|----
| **建立多個資料表**    |  使用單一 [`.create tables`](create-tables-command.md) 命令       | 不要發出許多 `.create table` 命令        | |
| **重新命名多個資料表**    | 進行單一呼叫[`.rename tables`](rename-table-command.md)        |  不要針對每一對資料表發出個別的呼叫   |    |
|**顯示命令**   |   使用最低範圍的 `.show` 命令 |   不在管道之後套用篩選（ `|` ）   </ul></li>  | 盡可能限制使用。 可能的話，請快取所傳回的資訊。 |
| 顯示範圍  | 使用`.show table T extents`   |不使用`.show cluster extents | where TableName == 'T'`  |
|  顯示資料庫架構。 |使用`.show database DB schema`  |  不使用`.show schema | where DatabaseName == 'DB'` |
| **在大型架構的叢集上顯示架構** <br> |使用[`.show databases schema`](../management/show-schema-database.md) |不使用`.show schema`| 例如，在具有超過100個資料庫的叢集上使用。
| **檢查資料表是否存在，或取得資料表的架構**|使用`.show table T schema as json`|不使用`.show table T` |請只使用此命令來取得單一資料表上的實際統計資料。|
| **定義將包含值之資料表的架構 `datetime`**  |將相關的資料行設定為 `datetime` 類型 | `string`在查詢時，請勿將或數值資料行轉換成以 `datetime` 進行篩選（如果可以在內嵌時間之前或期間進行）|
| **將範圍標籤新增至中繼資料** |謹慎使用 |避免 `drop-by:` 標記，這會限制系統在背景中執行效能導向清理程式的能力。|  <br> 請參閱[效能注意事項](../management/extents-overview.md#extent-tagging)。 |
