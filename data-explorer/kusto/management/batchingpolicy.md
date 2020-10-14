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
ms.openlocfilehash: 27c92b9081844df0e8f8d4dc207ba5d2a8a2e2f3
ms.sourcegitcommit: a10e7c6ba96bdb94d95ef23f5d1506eb8fda0041
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058660"
---
# <a name="ingestionbatching-policy"></a>IngestionBatching 原則

## <a name="overview"></a>總覽

在內嵌程式中，Kusto 會嘗試優化輸送量，方法是在其等候內嵌的同時批次處理小型輸入資料區塊。
這類批次處理可減少內嵌程式所耗用的資源，也不需要進行內嵌的資源，以優化非批次內嵌所產生的小型資料分區。

但有一個缺點，就是在內嵌之前執行批次處理，也就是引進強制延遲，讓端對端時間要求內嵌資料，直到它準備好進行查詢。

若要允許此項取捨的控制權，您可以使用此 `IngestionBatching` 原則。
此原則僅適用于已排入佇列的內嵌，並提供在將小型 blob 批次處理時允許的最大強制延遲。

## <a name="details"></a>詳細資料

如上所述，您可以大量內嵌資料的最佳大小。
目前該大小大約是 1 GB 的未壓縮資料。 在 blob 中完成的內嵌（保存的資料比最佳大小還低）是不理想的做法，因此在佇列的內嵌 Kusto 中，會將這類小型 blob 批次處理在一起。 批次處理會在第一個條件變成 true 之前完成：

1. 批次資料的總大小達到最佳大小，或
2. 達到原則所允許的最大延遲時間、大小總計或 blob 數目 `IngestionBatching`

您 `IngestionBatching` 可以在資料庫或資料表上設定原則。 根據預設，如果未定義原則，Kusto 會使用 **5 分鐘** 的預設值做為最大延遲時間、 **1000** 個專案、用於批次處理的 **1g** 總大小。

> [!WARNING]
> 將此原則設定為極小值的影響，就是叢集的 COGS 增加並降低效能。 此外，在限制的情況下，減少此值實際上可能會導致 **更** 有效率的端對端內嵌延遲，因為並行管理多個內嵌進程的額外負荷。

## <a name="additional-resources"></a>其他資源

* [IngestionBatching 原則命令參考](../management/batching-policy.md)
* [內嵌最佳作法-針對輸送量進行優化](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)
