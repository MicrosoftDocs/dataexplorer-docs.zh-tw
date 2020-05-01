---
title: Kusto IngestionBatching 原則可優化批次處理-Azure 資料總管
description: 本文說明 Azure 資料總管中的 IngestionBatching 原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 8f079fd7deb6e2b1ef81565aaf591ef4a5d0f020
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617743"
---
# <a name="ingestionbatching-policy"></a>IngestionBatching 原則

## <a name="overview"></a>概觀

在內嵌程式期間，Kusto 會在等待內嵌時，將小型輸入資料區塊批次處理在一起，藉此將輸送量優化。
這種批次處理會減少內嵌進程所耗用的資源，而且不需要預先內嵌的資源，將非批次內嵌所產生的小型資料分區優化。

不過，有一個缺點是在內嵌之前執行批次處理，這是引進強制延遲的情況，因此端對端時間會要求內嵌資料，直到它準備好進行查詢為止。

若要允許控制這項取捨，可以使用`IngestionBatching`原則。
此原則僅適用于排入佇列的內嵌，並提供在同時批次處理小型 blob 時允許的最大強制延遲。

## <a name="details"></a>詳細資料

如上所述，有一個要大量內嵌的最佳資料大小。
目前該大小大約是 1 GB 的未壓縮資料。 在 blob 中所進行的內嵌作業，會保留比最佳大小更少的資料，因此在佇列的內嵌 Kusto 中，會將這類小型 blob 一起批次處理。 批次處理會在第一個條件變成 true 之前完成：

1. 批次資料的總大小達到最佳大小，或
2. 已達到`IngestionBatching`原則所允許的最大延遲時間、總大小或 blob 數目

您`IngestionBatching`可以在資料庫或資料表上設定此原則。 根據預設，如果未定義 [原則]，Kusto 會使用**5 分鐘**的預設值做為最大延遲時間（ **1000**個專案）、 **1g**的總大小（用於批次處理）。

> [!WARNING]
> 建議要設定此原則的客戶先與 Kusto ops 小組聯繫。 將此原則設定為極小的值會造成叢集的 COGS 增加並降低效能。 此外，在此限制中，減少此值實際上可能會**導致有效的**端對端內嵌延遲，因為並行管理多個內嵌進程的額外負荷。

## <a name="additional-resources"></a>其他資源

* [IngestionBatching 原則命令參考](../management/batching-policy.md)
* [內嵌最佳做法-針對輸送量進行優化](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)