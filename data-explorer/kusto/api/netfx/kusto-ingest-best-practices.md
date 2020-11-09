---
title: Kusto 內嵌用戶端程式庫的最佳做法-Azure 資料總管
description: 本文說明 Kusto 內嵌用戶端程式庫的最佳做法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: 1e7cbbb62c58b83690dd8eb272395aacffd3eda2
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373743"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Kusto 內嵌用戶端程式庫-最佳作法

## <a name="select-the-right-ingestclient-flavor"></a>選取正確的 IngestClient 類別

使用 [KustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)，這是建議的原生資料內嵌模式。 原因如下：
* 引擎停機期間（例如部署期間）不可能進行直接內嵌。 在佇列內嵌模式中，要求會保存到 Azure 佇列，而資料管理服務會視需要重試。
* 資料管理服務會使用內嵌要求來防止引擎超載。 例如，使用直接內嵌覆寫這個控制項可能會嚴重影響引擎內嵌和查詢效能。
* 資料管理匯總多個內嵌要求。 匯總會優化要建立之初始分區 (範圍) 的大小。
* 取得有關每個內嵌的意見反應很簡單。

## <a name="avoid-tracking-ingest-operation-status"></a>避免追蹤內嵌作業狀態

[追蹤](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) 內嵌作業狀態很有用。 不過，對於大型磁片區資料流程，應避免針對每個內嵌要求開啟正面的通知。 這類追蹤會對基礎 xStore 資源產生極大的負載，這些資源可能會導致內嵌延遲增加，甚至是完整的叢集非回應性。

## <a name="optimizing-for-throughput"></a>優化輸送量

如果是在大型區塊中進行，則內嵌的效果最好。 
* 它會耗用最少的資源
* 它會產生最多 COGS 的 () 優化資料分區銷售的商品成本，並產生最佳的資料交易

我們建議使用 Kusto 內嵌程式庫或直接將資料內嵌到引擎，以將資料以 **100 MB** 的批次傳送至 **1 GB (未壓縮** 的客戶) 
* 當直接使用引擎時，上限是很重要的，有助於減少內嵌進程所使用的記憶體數量。 

> [!NOTE]
> 使用類別時 `KustoQueuedIngestClient` ，過度大量的資料區塊會分割成較小的區塊，而這些社區塊將會匯總到特定程度，然後才到達引擎。

* 內嵌資料大小的下限也很重要，但較不重要。 每次都能以小型批次擷取資料，然後完全沒問題，不過使用較大的批次會稍微降低效率。 此 `KustoQueuedIngestClient` 類別也可解決需要內嵌大量資料的客戶問題，並在將資料傳送到引擎之前，無法將它們批次處理成大型區塊。

## <a name="other-factors-that-impact-ingestion-throughput"></a>影響內嵌輸送量的其他因素

有多個因素會影響內嵌輸送量。 規劃您的內嵌管線時，請務必評估下列點，這可能會對您的 COGs 產生顯著的影響。

| 考慮因素 |  Description                                                                                              |
|--------------------------|-----------------------------------------------------------------------------------------------------------|
| 資料格式              | CSV 是最快速的內嵌格式。 針對相同的資料量，JSON 通常會花費2x 或3倍的時間。|
| 資料表寬度              | 請確定您只內嵌真正需要的資料。 資料表的範圍愈大，就需要編碼和編制索引的資料行越多，輸送量就越低。 您可以藉由提供內嵌對應來控制哪些欄位取得內嵌。       |
| 來源資料位置     | 避免跨區域讀取以加速內嵌。                                                       |
| 叢集中的負載      | 當叢集遇到高查詢負載時，內嵌需要較長的時間來完成，以降低輸送量。|

## <a name="optimizing-for-cogs"></a>針對 COGS 進行優化

使用 Kusto 用戶端程式庫將資料內嵌到 Azure 資料總管維持最便宜且最健全的選項。 我們強烈建議客戶複習他們的內嵌方法，以針對 COGS (銷售) 的商品成本優化，並充分利用將使 blob 交易大幅符合成本效益的 Azure 儲存體定價。

* **限制內嵌資料區塊的數目** 。
    若要更妥善掌控您的 Azure 資料總管內嵌成本，並減少每月帳單，請將內嵌資料區塊數目限制 (檔案/blob/資料流程) 。
* **將大型資料區塊內嵌 (多達1gb 的未壓縮資料)** 。 
    許多團隊都嘗試擷取數萬個小型資料區塊來達到低延遲，而這種資料的效率並不高。 
* **批次處理** 。 用戶端上任何數量的批次處理都會改善優化。 
* **提供 Kusto 完整、未壓縮資料大小的內嵌用戶端** 。
    若未這麼做，可能會導致額外的儲存體交易。
* **避免** 傳送將 `FlushImmediately` 旗標設為的內嵌資料 `true` 。 此外，請避免使用已設定的標記來傳送社區塊 `ingest-by` / `drop-by` 。 如果您使用這些方法，他們將會：
     * 防止服務在內嵌期間正確匯總資料
     * 在內嵌之後造成不必要的儲存體交易
     * 影響 COGS 
     * 如果您的叢集過度使用，可能會導致叢集的內嵌或查詢效能降級
