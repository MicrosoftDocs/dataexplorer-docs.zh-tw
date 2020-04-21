---
title: Kusto 引入用戶端庫 ─ 最佳實作 - Azure 資料資源管理員 ( Azure) 的資料資源管理員 。微軟文件
description: 本文介紹 Kusto 引入用戶端庫 - Azure 數據資源管理器中的最佳做法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: e28ec3f99abe4163b83721d753da98c0035d1e46
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523693"
---
# <a name="kusto-ingest-client-library---best-practices"></a>庫斯特引入用戶端庫 ─最佳實務

## <a name="choosing-the-right-ingestclient-flavor"></a>選擇正確的引入客戶風味
使用[KustoQueueingestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)是推薦的本機數據引入模式。 原因如下：
* 在 Kusto 引擎停機期間(例如部署期間),無法直接引入,而在排隊引入模式下,請求將保存到 Azure 佇列,數據管理服務將根據需要重試。
* 數據管理服務負責不要使用引入請求使引擎過載。 重寫此控制件(例如使用直接引入)可能會嚴重影響發動機性能,包括引入和查詢。
* 數據管理彙總多個引入請求,以最佳化要創建的初始分片(範圍)的大小。
* 有一種方便的方式來獲得關於每種攝入的反饋 - 無論是否成功。

## <a name="tracking-ingest-operation-status"></a>追蹤引入操作狀態
[跟蹤引入操作狀態](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)是 Kusto 中的一個有用功能,但是,打開它以進行成功報告很容易被濫用到會削弱您的服務的程度。<BR>

> [!WARNING]
> 應避免為大容量數據流的每個引入請求打開正通知,因為這會對底層的 xStore 資源造成極大的負載,>这可能导致引入延迟增加,甚至完全群集無回應性。

## <a name="optimizing-for-throughput"></a>優化輸送量
* 攝取效果最佳(也就是說,在攝入過程中消耗的資源最少,生成最優化的 COGS 優化的數據分片,併產生性能最佳的數據工件),如果以大塊完成。 通常,我們建議使用 Kusto.Ingest 函式庫或直接輸入引擎的客戶以**100 MB 到 1 GB(未壓縮)** 批次傳送資料
* 當直接使用 Kusto 引擎來幫助減少引入過程使用的記憶體量時,上限很重要`KustoQueuedIngestClient`(使用類時,用戶端上過大的數據塊將拆分為更小的部分,在到達 Kusto 引擎之前,小塊塊將在一定程度上聚合)
* 引入數據大小的下限也很重要,儘管不太重要。 不時地分小批攝收數據是完全沒問題的,儘管效率比使用大批量略低。 `KustoQueuedIngestClient`類還解決了客戶的問題,他們需要攝送大量數據,並且無法將它們批處理到大塊中,然後再將它們發送到 Kusto

## <a name="factors-impacting-ingestion-throughput"></a>影響攝入輸送量的因素
多種因素會影響攝入輸送量。 規劃 Kusto 引入管道時,請確保評估以下點,這些點可能對您的 COG 產生重大影響。
* 資料格式 - CSV 是引入的最快格式,JSON 通常需要 x2 或 x3 更長的時間才能用於相同資料量
* 表寬度 - 確保只引入您真正需要的數據,因為表越寬,對列進行編碼和索引越多,因此吞吐量越低。
    通過提供引入映射,可以控制哪些欄位被引入。
* 來源資料位置 - 避免跨區域讀取,從而加快引入速度
* 叢集上的負載 - 當群集遇到高查詢負載時,引入需要更長的時間才能完成,從而降低輸送量
* 引入模式 - 當群集以高達 1GB 的批次提供時,攝入處於`KustoQueuedIngestClient`最佳狀態(使用 時注意)

## <a name="optimizing-for-cogs"></a>最佳化 COGS
雖然使用 Kusto 用戶端庫將數據引入 Kusto 仍然是最便宜、最可靠的選項,但我們敦促我們的客戶重新審視其採用策略,並考慮到 Azure 儲存定價最近(2017 年秋季)的變化,這些更改使 Blob 事務(+x100)成本更高。
<BR>
請務必瞭解,發送到 Kusto 的數據塊(檔/blob/流)越多,每月帳單就越大。
如果您遵循以下建議,您將能夠更好地控制您的 Kusto 攝取成本:
* **更喜歡引入更大的資料塊(最多 1GB 的未壓縮資料)**<br>
    許多團隊試圖通過引入數千萬小塊數據來實現低延遲,這些數據效率極低且成本高昂。<br>
    用戶端上的任何批處理都會有所説明。 
* **確保您為 Kusto.Ingest 客戶端提供準確的未壓縮資料大小**<br>
    不這樣做可能會導致 Kusto 執行額外的存儲事務。
* **避免**將資料傳送用於引入`FlushImmediately`, 將標誌`true`設定為 ,或`ingest-by`/`drop-by`發送帶有 標記集的社區塊。<br>
    使用這些功能可防止 Kusto 服務在引入過程中正確聚合資料,並在引入後導致不必要的存儲事務,從而影響 COGS。<br>
    此外,過度使用這些可能導致群集中的引入和/或查詢性能下降。<br>
    