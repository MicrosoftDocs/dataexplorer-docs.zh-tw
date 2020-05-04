---
title: 跨叢集聯結-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的跨叢集聯結。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: bd2ebaa35de1997a96c6646c0fe0f7e248af2240
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737312"
---
# <a name="cross-cluster-join"></a>跨叢集聯結

::: zone pivot="azuredataexplorer"

如需跨叢集查詢的一般討論，請參閱[跨叢集或跨資料庫查詢](cross-cluster-or-database-queries.md)

您可以在位於不同叢集的資料集上執行聯結作業。 例如： 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

在上述範例中，聯結作業是一個跨叢集聯結，假設目前的叢集既不是 "SomeCluster" 也不是 "SomeCluster2"。

請注意，下列範例中的

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

聯結作業不是跨叢集聯結，因為它的兩個運算元都是源自相同的叢集。

當 Kusto 遇到跨叢集聯結時，會自動決定要在何處執行聯結作業。 這項決策可以有三個可能結果的其中一個：
* 在左運算元的叢集上執行聯結作業，將會先由這個叢集提取右運算元。 （聯結範例 **（1）** 將會在本機叢集上執行）
* 在右運算元的叢集上執行聯結作業，此叢集會先提取左運算元。 （聯結範例 **（2）** 將會在 "SomeCluster2" 上執行）
* 在本機執行聯結作業（表示在收到查詢的叢集上），兩個運算元都會由本機叢集第一次提取

實際的決策取決於特定查詢，自動加入遠端處理策略為（簡化版本）：「如果其中一個運算元是本機的，join 將會在本機執行。 如果兩個運算元都是遠端聯結，則會在右運算元的叢集上執行 "。

如果未遵循自動遠端處理策略，有時可以大幅改善查詢的效能。 一般來說，這是最好的（從效能的觀點來看），以便在最大運算元的叢集上執行聯結作業。

在範例中 **（1）** 所產生的```T | ...``` it 資料集， ```cluster("SomeCluster").database("SomeDB").T2 | ...```比在 "SomeCluster" 上執行聯結作業更有效率。

這可以藉由提供 Kusto join 遠端提示來達成。 語法為：

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

以下是的合法值*`strategy`*
* **`left`**-在左運算元的叢集上執行聯結 
* **`right`**-在右運算元的叢集上執行聯結
* **`local`**-在目前叢集的叢集上執行聯結
* **`auto`**-（預設值）讓 Kusto 進行自動遠端執行決策

**注意：** 如果提示的策略不適用於聯結作業，則 Kusto 會忽略聯結遠端提示。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
