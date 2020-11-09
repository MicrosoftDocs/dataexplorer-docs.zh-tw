---
title: Kusto 內嵌用戶端程式庫-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Kusto 內嵌用戶端程式庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/18/2020
ms.openlocfilehash: 33d3515a8465a1e9c3397e675a51c95aa3f00d42
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373828"
---
# <a name="kusto-ingest-client-library"></a>Kusto 內嵌用戶端程式庫 

`Kusto.Ingest` 程式庫是用來將資料傳送至 Kusto 服務的 .NET 4.6.2 程式庫。
它會依賴下列程式庫和 Sdk：

* Azure AD 驗證的 ADAL
* Azure 儲存體用戶端

內嵌方法是由 [IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient) 介面所定義。  這些方法會在同步和非同步模式中，處理來自資料流程、IDataReader、本機檔案和 Azure blob 的資料內嵌。

## <a name="ingest-client-flavors"></a>內嵌用戶端類別

內嵌用戶端有兩種基本的類別：已排入佇列和直接。

### <a name="queued-ingestion"></a>已排入佇列的內嵌

由 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)定義的佇列內嵌模式，會限制 Kusto 服務的用戶端程式代碼相依性。 內嵌的完成方式是將 Kusto 內嵌訊息張貼到 Azure 佇列，然後從 Kusto 資料管理 (內嵌) 服務取得該佇列。 內嵌用戶端會使用來自 Kusto 資料管理服務的資源來建立任何中繼儲存體專案。

**佇列模式的優點包括：**

* 從 Kusto 引擎服務分離資料內嵌進程
* 當 Kusto 引擎 (或內嵌) 服務無法使用時，讓內嵌要求保存
* 藉由內嵌服務有效率且可控制輸入資料的匯總來改善效能 
* 讓 Kusto 內嵌服務管理 Kusto 引擎服務上的內嵌負載
* 在暫時性的內嵌失敗時（例如針對 XStore 節流），視需要重試 Kusto 內嵌服務
* 提供便利的機制，以追蹤每個內嵌要求的進度和結果

下圖概述與 Kusto 進行佇列的內嵌用戶端互動：

:::image type="content" source="../images/about-kusto-ingest/queued-ingest.png" alt-text="此圖顯示 Kusto 內嵌程式庫如何將查詢傳送至查詢內嵌模式下的 Kusto 服務。":::
 
### <a name="direct-ingestion"></a>直接內嵌

由 IKustoDirectIngestClient 所定義的直接內嵌模式，會強制與 Kusto 引擎服務直接互動。 在此模式中，Kusto 內嵌服務不會仲裁或管理資料。 每個內嵌要求最後都會轉譯成 `.ingest` 直接在 Kusto 引擎服務上執行的命令。

下圖概述與 Kusto 的直接內嵌用戶端互動：

:::image type="content" source="../images/about-kusto-ingest/direct-ingest.png" alt-text="此圖顯示 Kusto 內嵌程式庫如何以直接內嵌模式將查詢傳送到 Kusto 服務。":::

> [!NOTE]
> 不建議在生產等級內嵌解決方案中使用直接模式。

**直接模式的優點包括：**

* 低延遲且沒有匯總。 不過，您也可以使用佇列內嵌來達成低延遲
* 使用同步方法時，方法完成表示內嵌作業結束

**直接模式的缺點包括：**

* 用戶端程式代碼必須執行任何重試或錯誤處理邏輯
* Kusto 引擎服務無法使用時，不可能發生內嵌
* 用戶端程式代碼可能會造成 Kusto 引擎服務無法使用內嵌要求，因為它並不知道引擎服務的容量

## <a name="ingestion-best-practices"></a>內嵌最佳作法

內嵌[最佳做法](kusto-ingest-best-practices.md)可提供 COGS (銷售的商品成本) 和內嵌的輸送量 POV。

* **執行緒安全-** Kusto 內嵌用戶端實作為安全線程，可供重複使用。 不需要為 `KustoQueuedIngestClient` 每個或數個內嵌作業建立類別的實例。 `KustoQueuedIngestClient`每個使用者進程的每個目標 Kusto 叢集都需要單一實例。 執行多個實例會有生產力，而且可能會在資料管理叢集上造成 DoS。

* **支援的資料格式-** 使用原生內嵌時（如果尚未存在），請將資料上傳至一或多個 Azure 儲存體 blob。 目前支援的 blob 格式記載于 [支援的資料格式](../../../ingestion-supported-formats.md)。

* **架構對應-** 
[架構](../../management/mappings.md)對應有助於將源資料欄位確定性地系結至目的地資料表資料行。

* 內嵌 **許可權-** 
[Kusto 內嵌許可權](kusto-ingest-client-permissions.md)說明使用封裝成功內嵌所需的許可權設定 `Kusto.Ingest` 。

* **使用方式-** 如先前所述，Kusto 的持續性和高規模內嵌解決方案的建議基礎應為 **KustoQueuedIngestClient** 。
為了將 Kusto 服務的不必要負載降到最低，我們建議您針對每個 Kusto 叢集，使用 Kusto 內嵌用戶端的單一實例 (排入佇列或直接) 每個進程。 Kusto 內嵌用戶端實作為安全線程，而且可完全重新進入。

## <a name="next-steps"></a>下一步

* [Kusto：內嵌用戶端參考](kusto-ingest-client-reference.md) 包含 Kusto 內嵌用戶端介面和實作為的完整參考。 您可以找到有關如何建立內嵌用戶端、增強內嵌要求、管理內嵌進度等等的資訊。

* [Kusto](kusto-ingest-client-status.md) 內嵌作業狀態說明追蹤內嵌狀態的 KustoQueuedIngestClient 功能

* [Kusto：內嵌錯誤](kusto-ingest-client-errors.md) 描述 Kusto 內嵌用戶端錯誤和例外狀況

* [Kusto 內嵌範例](kusto-ingest-client-examples.md) 會顯示程式碼片段，示範擷取資料到 Kusto 中的各種技術

* [未 Kusto 的資料內嵌：](kusto-ingest-client-rest.md)說明如何使用 Kusto REST api，以及不相依于程式庫，來執行排入佇列的 Kusto 內嵌 `Kusto.Ingest`

