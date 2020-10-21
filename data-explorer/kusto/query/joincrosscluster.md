---
title: 跨叢集聯結-Azure 資料總管
description: 本文說明 Azure 資料總管中的跨叢集聯結。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a7c8f89886a8c12941dbc218ad69b35eebd7f1c7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246599"
---
# <a name="cross-cluster-join"></a>跨叢集聯結

::: zone pivot="azuredataexplorer"

如需跨叢集查詢的一般討論，請參閱 [跨叢集或跨資料庫的查詢](cross-cluster-or-database-queries.md)

您可以在位於不同叢集的資料集上進行聯結作業。 例如：

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

在上述範例中，聯結作業是一種跨叢集聯結，假設目前的叢集不是 "SomeCluster" 或 "SomeCluster2"。

在下例中︰

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

聯結作業不是跨叢集聯結，因為它的兩個運算元都源自相同的群集。

當 Kusto 遇到跨叢集聯結時，它會自動決定要在何處執行聯結作業本身。 這項決定可以有三個可能的結果之一：

* 在左運算元的叢集上執行聯結作業，此叢集將會先提取 right 運算元。 範例中的 (聯結 ** (1) ** 將在本機叢集上執行) 
* 在右運算元的叢集上執行聯結作業，此叢集會先提取左運算元。 範例中的 (聯結 ** (2) ** 將在 "SomeCluster2" 上執行 ) 
* 在本機執行聯結作業 (意義是在收到查詢) 的叢集上，兩個運算元都會先由本機叢集提取。

實際的決策取決於特定的查詢。 自動聯結遠端處理策略是 (簡化的版本) ：「如果其中一個運算元是本機，則會在本機執行聯結。 如果這兩個運算元都是遠端的，則會在右運算元的叢集上執行聯結。

如果未遵循自動遠端處理策略，有時可以改善查詢的效能。 在此情況下，請對最大運算元的叢集執行聯結作業。

例如 ** (1) ** 所產生的資料集遠小於所產生的資料集 `T | ...` `cluster("SomeCluster").database("SomeDB").T2 | ...` ，在 "SomeCluster" 上執行聯結作業會更有效率。

您可以藉由提供 Kusto 聯結遠端提示來完成這項作業。 語法為：

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

以下是的合法值 `strategy`
* `left` -在左運算元的叢集中執行聯結 
* `right` -在右運算元的叢集中執行聯結
* `local` -在目前叢集中的叢集上執行聯結
* `auto` - (預設值) 讓 Kusto 進行自動遠端的決策

> [!Note]
> 如果提示的策略不適用於聯結作業，Kusto 就會忽略聯結遠端提示。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
