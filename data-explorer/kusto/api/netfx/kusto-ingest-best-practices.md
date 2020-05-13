---
title: Kusto 內嵌用戶端程式庫-最佳做法-Azure 資料總管
description: 本文說明 Kusto 內嵌用戶端程式庫的最佳做法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: 615bb1ef9400f3671b88e2108d4e6cad0469978b
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373682"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Kusto 內嵌用戶端程式庫-最佳作法

## <a name="select-the-right-ingestclient-flavor"></a>選取正確的 IngestClient 類別

使用[KustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)，這是建議的原生資料內嵌模式。 原因如下：
* 引擎停機期間不可能進行直接內嵌，例如在部署期間。 在佇列的內嵌模式中，要求會保存到 Azure 佇列，而資料管理服務會視需要重試。
* 資料管理服務會使用內嵌要求來防止引擎超載。 例如，使用直接內嵌來覆寫此控制項，可能會嚴重影響引擎內嵌和查詢效能。
* 資料管理會匯總多個內嵌要求。 匯總會將要建立的初始分區（範圍）大小優化。
* 您可以輕鬆地取得每個內嵌的意見反應。

## <a name="avoid-tracking-ingest-operation-status"></a>避免追蹤內嵌作業狀態

[追蹤](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)內嵌作業狀態很有用。 不過，針對大型磁片區資料流程，應避免為每個內嵌要求開啟正面通知。 這類追蹤會對基礎 xStore 資源帶來極大的負載，這可能會導致內嵌延遲增加，甚至完成叢集非回應能力。

## <a name="optimizing-for-throughput"></a>針對輸送量進行優化

如果在大型區塊中執行，內嵌的效果最佳。 
* 它會耗用最少的資源
* 它會產生最多 COGS 優化的資料分區，並產生最佳的資料交易

我們建議使用 Kusto 程式庫或直接將資料內嵌至引擎的客戶，以**100 MB**的批次傳送資料到**1 GB （未壓縮）**
* 當直接使用引擎時，上限是很重要的，有助於減少內嵌進程所使用的記憶體數量 

> [!NOTE]
> 使用類別時 `KustoQueuedIngestClient` ，過度大量的資料區塊會分割成較小的區塊，而且這些社區塊會匯總到特定程度，然後才到達引擎。

* 內嵌資料大小的下限也很重要，但較不重要。 以小型批次的方式內嵌資料，然後就能順利進行，雖然效率比使用大型批次稍微低一點。 `KustoQueuedIngestClient`類別也可以針對需要內嵌大量資料的客戶解決問題，而且在將它們傳送至引擎之前，無法將它們批次處理成大型區塊。

## <a name="other-factors-that-impact-ingestion-throughput"></a>影響內嵌輸送量的其他因素

有多個因素會影響內嵌的輸送量。 在規劃您的內嵌管線時，請務必評估下列幾點，這可能會對您的 COGs 產生顯著影響。

| 考慮因素 |  描述                                                                                              |
|--------------------------|-----------------------------------------------------------------------------------------------------------|
| 資料格式              | CSV 是最快速內嵌的格式。 對於相同的資料量，JSON 通常會花費2倍或3倍的時間。|
| 資料表寬度              | 請確定您只內嵌您真正需要的資料。 資料表的範圍愈多，需要編碼和編制索引的資料行越多，輸送量就越低。 您可以藉由提供內嵌對應來控制哪些欄位會取得內嵌。       |
| 來源資料位置     | 避免跨區域讀取以加速內嵌。                                                       |
| 在叢集上載入      | 當叢集遇到高查詢負載時，擷取將需要較長的時間來完成，進而減少輸送量。|

## <a name="optimizing-for-cogs"></a>針對 COGS 優化

使用 Kusto 用戶端程式庫將資料內嵌至 Azure 資料總管仍是最便宜的選項。 我們強烈建議客戶審查其內嵌方法，並利用可讓 blob 交易大幅符合成本效益的 Azure 儲存體定價。

* **限制內嵌資料區塊的數目**。
    為了更有效地控制您的 Azure 資料總管內嵌成本並減少每月帳單，請限制內嵌資料區塊（檔案/blob/資料流程）的數目。
* 內嵌**大量的資料（最多1gb 的未壓縮資料）**。 
    許多小組都藉由內嵌數十萬個小型資料區塊來嘗試達到低延遲，但效率不高，而且成本高昂。 
* **批次處理**。 在用戶端任何數量的批次處理都會改善優化。 
* **以精確、未壓縮的資料大小提供 Kusto 的內嵌用戶端**。
    若未這麼做，可能會造成額外的儲存體交易。
* **避免**傳送資料進行內嵌，並將 `FlushImmediately` 旗標設為 `true` 。 此外，請避免傳送已設定標記的小型區塊 `ingest-by` / `drop-by` 。 如果您使用這些方法，他們將會：
     * 防止服務在內嵌期間適當地匯總資料
     * 在內嵌之後導致不必要的儲存體交易
     * 影響 COGS
     * 若過度使用，可能會導致叢集的內嵌或查詢效能變差
