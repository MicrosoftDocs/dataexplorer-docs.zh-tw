---
title: 引入批次處理策略 - Azure 資料資源管理員 :微軟文件
description: 本文介紹 Azure 數據資源管理器中的引入批處理策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: fa3a4cfd512e3e37ba6b6abf8f8316d6ff12fdec
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522231"
---
# <a name="ingestionbatching-policy"></a>引入批次處理原則

## <a name="overview"></a>概觀

在引入過程中,Kusto 嘗試通過將小入口數據塊批次處理在一起以等待引入來優化輸送量。
這種批處理減少了攝入過程消耗的資源,並且不需要后引入資源來優化非批處理引入產生的小數據分片。

但是,在引入之前執行批處理有一個缺點,即引入強制延遲,以便從請求引入數據到準備好查詢的端到端時間更大。

為了控制這種權衡,可以使用策略`IngestionBatching`。
此策略僅應用於排隊引入,並提供最大強制延遲,以便在將小 Blob 批處理在一起時允許。

## <a name="details"></a>詳細資料

如上所述,有一個最佳大小的數據要批量攝入。
目前,該大小約為 1 GB 的未壓縮數據。 在保存數據比最佳大小少得多的 Blob 中完成的引入不是最佳的,因此在排隊的引入 Kusto 中,將此類小 Blob 批次處理在一起。 批次處理將完成,直到第一個條件變為 true:

1. 批次處理資料的總大小達到最佳大小或
2. 達到`IngestionBatching`策略允許的最大延遲時間、總大小或 blob 數

可以在`IngestionBatching`資料庫或表上設置策略。 默認情況下,如果不定義策略,Kusto 將使用**5 分鐘的**預設值作為最大延遲時間 **,1000**個專案,總大小**為 1G**進行批處理。

> [!WARNING]
> 建議想要設定此策略的客戶首先聯繫 Kusto 運營團隊。 將此策略設置為非常小的值的影響是群集的 COGS 增加,並降低性能。 此外,在限制中,由於並行管理多個引入過程的開銷,減小此值實際上可能會導致**有效的**端到端引入延遲增加。

## <a name="additional-resources"></a>其他資源

* [引入批次處理原則指令參考](../management/batching-policy.md)
* [引入最佳實作 - 最佳化](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)