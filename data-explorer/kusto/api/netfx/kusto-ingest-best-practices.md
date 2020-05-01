---
title: Kusto 內嵌用戶端程式庫-最佳做法-Azure 資料總管 |Microsoft Docs
description: 本文說明 Kusto 內嵌用戶端程式庫-Azure 資料總管中的最佳作法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: 2369d352e316f1d9b49de71fb197507280598754
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617930"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Kusto 內嵌用戶端程式庫-最佳作法

## <a name="choosing-the-right-ingestclient-flavor"></a>選擇正確的 IngestClient 類別

建議使用[KustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)來進行原生資料內嵌模式。 原因如下：
* 在引擎停機期間（例如在部署期間）不可能進行直接內嵌，而在佇列的內嵌模式中，要求會保存到 Azure 佇列，而資料管理服務會視需要重試。
* 資料管理服務會負責不多載具有內嵌要求的引擎。 覆寫此控制項（例如使用直接內嵌）可能會嚴重影響引擎效能，包括內嵌和查詢。
* 資料管理會匯總多個內嵌要求，以優化要建立之初始分區（範圍）的大小。
* 取得每個內嵌的意見反應很容易。

## <a name="tracking-ingest-operation-status"></a>追蹤內嵌作業狀態

[追蹤](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)內嵌作業狀態是很有用的功能。 不過，將其開啟以報告成功，很容易就會被濫用到它將會讓您的服務癱瘓的程度。

> [!WARNING]
> 應避免針對大型磁片區資料流程的每個內嵌要求開啟正面通知，因為這會對基礎 xStore 資源造成極大的負載。 額外的負載可能會導致內嵌延遲增加，甚至完成叢集非回應能力。

## <a name="optimizing-for-throughput"></a>針對輸送量進行優化

* 如果在大型區塊中執行，內嵌的效果最佳。 它會耗用最少的資源，產生最多 COGS 優化的資料分區，並產生效能最佳的資料成品。 一般而言，我們建議使用 Kusto 程式庫或直接將資料內嵌至引擎的客戶，以 100 MB 的批次傳送資料**到 1 GB （未壓縮）**
* 當直接使用引擎時，上限是很重要的，有助於減少內嵌進程所使用的記憶體數量 

> [!NOTE]
> 使用`KustoQueuedIngestClient`類別時，過度大量的資料區塊會分割成較小的區塊，而且這些社區塊會匯總到特定程度，然後才到達引擎。

* 內嵌資料大小的下限也很重要，但較不重要。 以小型批次的方式內嵌資料，然後就能順利進行，雖然效率比使用大型批次稍微低一點。 `KustoQueuedIngestClient`類別也可解決需要內嵌大量資料，且在將它們傳送至引擎之前無法將其批次處理成大型區塊的客戶所遇到的問題。

## <a name="factors-impacting-ingestion-throughput"></a>影響內嵌輸送量的因素

有多個因素會影響內嵌的輸送量。 在規劃您的內嵌管線時，請務必評估下列幾點，這可能會對您的 COGs 產生顯著影響。

| 考慮因素 |  Decription                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------|
| 資料格式              | CSV 是最快的內嵌格式，JSON 通常會在相同的資料量中花費2倍或3倍以上的時間。|
| 資料表寬度              | 請確定您只內嵌您真正需要的資料。 資料表的範圍愈多，需要編碼和編制索引的資料行越多，因此輸送量就越低。 您可以藉由提供內嵌對應來控制哪些欄位會取得內嵌。|
| 來源資料位置     | 避免跨區域讀取以加速內嵌。                                                       |
| 在叢集上載入      | 當叢集遇到高查詢負載時，擷取將需要較長的時間來完成，進而減少輸送量。|
| 內嵌模式        | 當叢集以高達1GB 的批次（使用`KustoQueuedIngestClient`來處理）提供服務時，內嵌就是最佳的。 |

## <a name="optimizing-for-cogs"></a>針對 COGS 優化

使用 Kusto 用戶端程式庫將資料內嵌至 Kusto，仍然是最便宜的選項。 我們強烈建議客戶審查其內嵌方法，並利用可讓 blob 交易大幅符合成本效益的 Azure 儲存體定價。

為了更有效地控制您的 Azure 資料總管內嵌成本並減少每月帳單，請限制內嵌資料區塊（檔案/blob/資料流程）的數目。

* **偏好內嵌較大區塊的資料（最多1gb 的未壓縮資料）**。 
    許多小組嘗試內嵌數十個數百萬個社區塊的資料來達到低延遲，這種方式極不有效率，而且成本高昂。 用戶端上任何數量的批次處理都有説明。 
* **請確定您是以正確、未壓縮的資料大小提供 Kusto 的用戶端。**
    若未這麼做，可能會造成額外的儲存體交易。
* **避免**傳送資料進行內嵌，並`FlushImmediately`將旗標`true`設為，或傳送已`ingest-by` / `drop-by`設定標記的小型區塊。
    使用這些方法可防止服務在內嵌期間適當地匯總資料，並在內嵌之後導致不必要的儲存體交易，因而影響 COGS。
    
    此外，過度使用這些可能會導致叢集的內嵌和/或查詢效能降低。
    
