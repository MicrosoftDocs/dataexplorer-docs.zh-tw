---
title: 跨群集聯接 - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的跨群集聯接。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1199b148fa295ac17417bbf590a73bfc9400a710
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765929"
---
# <a name="cross-cluster-join"></a>跨群集聯結

::: zone pivot="azuredataexplorer"

有關跨群集查詢的一般討論,請參閱[跨群集查詢或跨資料庫查詢](cross-cluster-or-database-queries.md)

可以對駐留在不同群集上的數據集執行聯接操作。 例如： 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

在上面的示例中,聯接操作是跨群集聯接,假定當前群集既不是"某個群集",也不是"某個群集2"。

請注意,在以下範例中

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

聯接操作不是跨群集聯接,因為它的兩個操作數都源自同一個群集。

當 Kusto 遇到跨群集聯接時,它將自動決定在何處執行聯接操作本身。 此決策可以產生三種可能的結果之一:
* 在左側操作數的群集上執行聯接操作,此群集將首先獲取右操作數。 ( 加入範例 **(1)** 將在本地群集上執行 )
* 在右操作數的群集上執行聯接操作,此群集將首先獲取左操作數。 (加入範例 **(2)** 將在「某個群集 2」上執行)
* 在本地執行聯接操作(這意味著在接收查詢的群集上),兩個操作數將首先由本地群集獲取

實際決策取決於特定查詢,自動聯接遠端處理策略為(簡化版本):如果其中一個操作數是本地操作數,則聯接將在本地執行。 如果兩個操作數都是遠程聯接,將在右側操作數的群集上執行"。

有時,如果不遵循自動遠端處理策略,查詢的性能可以顯著提高。 一般來說,最好(從性能角度看)在最大操作數的群集上執行聯接操作。

如果在範例 **(1)**```T | ...```中, 它產生的數據```cluster("SomeCluster").database("SomeDB").T2 | ...```集比生成 資料集小得多,則對「某個群集」執行聯接操作的效率更高。

這可以通過給 Kusto 加入回位提示來實現。 語法為：

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

以下是*`strategy`*
* **`left`**- 在左方操作數的叢集上執行聯結 
* **`right`**- 在正確操作數的叢集上執行聯結
* **`local`**- 在目前群組的叢集上執行聯結
* **`auto`**- (預設)讓函式庫托做出自動遠端處理決定

**註:** 如果暗示策略不適用於聯接操作,Kusto 將忽略聯接回微提示。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
