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
ms.openlocfilehash: a5655ac982ab65d06cdee7c93a166dce51377eed
ms.sourcegitcommit: e66c5f4b833b4f6269bb7bfa5695519fcb11d9fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83630060"
---
# <a name="kusto-ingest-client-library"></a>Kusto 內嵌用戶端程式庫 

`Kusto.Ingest`程式庫是一種 .NET 4.6.2 程式庫，用於將資料傳送至 Kusto 服務。
它會依賴下列程式庫和 Sdk：

* Azure AD 驗證的 ADAL
* Azure 儲存體用戶端

內嵌方法是由[IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient)介面所定義。  這些方法會處理從資料流程、IDataReader、本機檔案和 Azure blob 在同步與非同步模式中內嵌的資料。

## <a name="ingest-client-flavors"></a>內嵌用戶端類別

內嵌用戶端有兩種基本的類別：已排入佇列和直接存取。

### <a name="queued-ingestion"></a>已排入佇列的內嵌

排入佇列的內嵌模式（由[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)定義）會限制 Kusto 服務上的用戶端程式代碼相依性。 透過將 Kusto 的內嵌訊息張貼到 Azure 佇列，然後從 Kusto 資料管理（內嵌）服務取得，即可完成內嵌作業。 內嵌用戶端會使用 Kusto 資料管理服務中的資源來建立任何中繼儲存體專案。

**佇列模式的優點包括：**

* 將資料內嵌程式與 Kusto 引擎服務分離
* 可讓您在 Kusto 引擎（或內嵌）服務無法使用時，內嵌要保存的要求。
* 藉由內嵌服務有效率且可控制輸入資料的匯總，來改善效能 
* 讓 Kusto 的內嵌服務管理 Kusto Engine 服務的內嵌負載
* 視需要重試 Kusto 的內嵌服務，以取得暫時性的內嵌失敗（例如 XStore 節流）
* 提供便利的機制來追蹤每個內嵌要求的進度和結果

下圖概述已排入佇列的內嵌用戶端與 Kusto 的互動：

:::image type="content" source="../images/about-kusto-ingest/queued-ingest.png" alt-text="已排入佇列-內嵌":::
 
### <a name="direct-ingestion"></a>直接內嵌

直接內嵌模式（由 IKustoDirectIngestClient 定義）會強制與 Kusto 引擎服務直接互動。 在此模式中，Kusto 的內嵌服務不會仲裁或管理資料。 每個內嵌要求最後都會轉譯成 `.ingest` 直接在 Kusto Engine 服務上執行的命令。

下圖概述直接內嵌用戶端與 Kusto 的互動：

:::image type="content" source="../images/about-kusto-ingest/direct-ingest.png" alt-text="直接內嵌":::

> [!NOTE]
> 對於生產等級的內嵌解決方案，不建議使用直接模式。

**Direct 模式的優點包括：**

* 低延遲且沒有匯總。 不過，也可以透過佇列內嵌來達成低延遲
* 使用同步方法時，方法完成會指出內嵌作業的結束

**Direct 模式的缺點包括：**

* 用戶端程式代碼必須執行任何重試或錯誤處理邏輯
* 當 Kusto Engine 服務無法使用時，無法進行擷取
* 用戶端程式代碼可能會因為內嵌要求而造成 Kusto 引擎服務不足，因為它並不知道引擎服務的容量

## <a name="ingestion-best-practices"></a>內嵌最佳做法

內嵌[最佳做法](kusto-ingest-best-practices.md)會在內嵌時提供 COGs 和輸送量 POV。

* **執行緒安全-** Kusto 內嵌用戶端執行是安全線程，並可供重複使用。 您不需要 `KustoQueuedIngestClient` 針對每個或數個內嵌作業，建立類別的實例。 每 `KustoQueuedIngestClient` 個使用者進程每個目標 Kusto 叢集都需要一個的單一實例。 執行多個實例的效率極高，而且可能會導致資料管理叢集上的 DoS。

* **支援的資料格式-** 使用原生內嵌時（如果尚未存在），請將資料上傳至一或多個 Azure 儲存體 blob。 目前支援的 blob 格式記載于[支援的資料格式](../../../ingestion-supported-formats.md)之下。

* **架構對應-** 
[架構](../../management/mappings.md)對應有助於以決定性的方式將源資料欄位系結至目的地資料表資料行。

* 內嵌**許可權-** 
[Kusto](kusto-ingest-client-permissions.md)的內嵌許可權說明使用封裝成功內嵌所需的許可權設定 `Kusto.Ingest` 。

* **使用方式-** 如先前所述，Kusto 的持續性和大規模內嵌解決方案建議的基礎應該是**KustoQueuedIngestClient**。
若要將 Kusto 服務上不必要的負載降到最低，建議您針對每個 Kusto 叢集，針對每個進程使用單一 Kusto 內嵌用戶端實例（佇列或直接）。 Kusto 內嵌用戶端執行是安全線程，而且完全可重新進入。

## <a name="next-steps"></a>後續步驟

* [Kusto：內嵌用戶端參考](kusto-ingest-client-reference.md)包含 Kusto 內嵌用戶端介面和執行的完整參考。 您可以找到有關如何建立內嵌用戶端、增加內嵌要求、管理內嵌進度等等的資訊。

* [Kusto 作業狀態](kusto-ingest-client-status.md)說明用於追蹤內嵌狀態的 KustoQueuedIngestClient 功能

* [Kusto。內嵌錯誤](kusto-ingest-client-errors.md)描述 Kusto 內嵌用戶端錯誤和例外狀況

* [Kusto 範例](kusto-ingest-client-examples.md)顯示程式碼片段，示範將資料內嵌到 Kusto 的各種技術

* [沒有 Kusto 的資料](kusto-ingest-client-rest.md)內嵌說明如何使用 Kusto REST api 來執行佇列 Kusto 內嵌，而不需依賴連結 `Kusto.Ingest` 庫
