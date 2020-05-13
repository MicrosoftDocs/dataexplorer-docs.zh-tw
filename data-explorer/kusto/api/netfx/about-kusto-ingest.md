---
title: Kusto 內嵌用戶端程式庫-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Kusto 內嵌用戶端程式庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/24/2020
ms.openlocfilehash: 5770c59ff7298567cad01bb3ed4cc6a684b2378a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373701"
---
# <a name="kusto-ingest-client-library"></a>Kusto 內嵌用戶端程式庫

## <a name="overview"></a>概觀
Kusto 程式庫是一種 .NET 4.6.2 程式庫，可讓您將資料傳送至 Kusto 服務。
Kusto 會依賴下列程式庫和 Sdk：

* AAD 驗證的 ADAL
* Azure 儲存體用戶端

Kusto 內嵌方法是由[IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient)介面所定義，可讓您從資料流程、IDataReader、本機檔案和 Azure blob，同時在同步和非同步模式中內嵌資料。

## <a name="ingest-client-flavors"></a>內嵌用戶端類別
就概念而言，內嵌用戶端有兩種基本的類別：已排入佇列和直接存取。

### <a name="queued-ingestion"></a>排入佇列的內嵌
此模式是由[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)所定義，會限制 Kusto 服務上的用戶端程式代碼相依性。 藉由將 Kusto 的內嵌訊息張貼至 Azure 佇列來執行內嵌作業，然後從 Kusto 資料管理（內嵌）服務取得。 內嵌用戶端會使用 Kusto 資料管理服務所配置的資源來建立任何中繼儲存體成品。

**佇列模式的優點**包括（但不限於）：

* 從 Kusto Engine 服務分離資料內嵌程式
* 允許在 Kusto 引擎（或內嵌）服務無法使用時保存內嵌要求
* 允許透過內嵌服務有效率且可控制輸入資料的匯總，改善效能
* 允許 Kusto 內嵌服務管理 Kusto Engine 服務的內嵌負載
* Kusto 的內嵌服務會視需要在暫時性的內嵌失敗時重試（例如，XStore 節流）
* 提供便利的機制來追蹤每個內嵌要求的進度和結果

下圖概述已排入佇列的內嵌用戶端與 Kusto 的互動：

![替代文字](../images/queued-ingest.jpg "已排入佇列-內嵌")

### <a name="direct-ingestion"></a>直接內嵌
此模式由 IKustoDirectIngestClient 定義，會強制與 Kusto 引擎服務直接互動。 在此模式中，Kusto 內嵌服務不會仲裁或管理資料。 Direct 模式中的每個內嵌要求最後都會轉譯成 `.ingest` 直接在 Kusto Engine 服務上執行的命令。
下圖概述直接內嵌用戶端與 Kusto 的互動：

![替代文字](../images/direct-ingest.jpg "直接內嵌")

> [!NOTE]
> 對於生產等級的內嵌解決方案，不建議使用直接模式。

**Direct 模式的優點**包括：

* 低度延遲（沒有匯總）。 不過，也可以透過佇列內嵌來達成低延遲
* 使用同步方法時，方法完成會指出內嵌作業的結束

**Direct 模式的缺點**包括：

* 用戶端程式代碼必須執行任何重試或錯誤處理邏輯
* 當 Kusto Engine 服務無法使用時，無法進行擷取
* 用戶端程式代碼可能會因為內嵌要求而造成 Kusto 引擎服務不足，因為它並不知道引擎服務的容量

## <a name="ingestion-best-practices"></a>內嵌最佳做法

### <a name="general"></a>一般
內嵌[最佳做法](kusto-ingest-best-practices.md)會在內嵌時提供 COGs 和輸送量 POV。

### <a name="thread-safety"></a>執行緒安全
Kusto 內嵌用戶端執行是安全線程，並可供重複使用。 您不需要 `KustoQueuedIngestClient` 針對每個或甚至數個內嵌作業，建立類別的實例。 每 `KustoQueuedIngestClient` 個使用者進程每個目標 Kusto 叢集都需要一個的單一實例。 執行多個實例會提高效率，而且可能會 DoS 資料管理叢集。

### <a name="supported-data-formats"></a>支援的資料格式
使用原生內嵌時（如果尚未存在），請將資料上傳至一或多個 Azure 儲存體 blob。 目前支援的 blob 格式記載于[支援的資料格式](../../../ingestion-supported-formats.md)主題中。

### <a name="schema-mapping"></a>結構描述對應
[架構](../../management/mappings.md)對應有助於以決定性的方式將源資料欄位系結至目的地資料表資料行。

## <a name="usage-and-further-reading"></a>使用方式和進一步閱讀

* 如上所述，Kusto 的持續性和大規模內嵌解決方案建議的基礎是**KustoQueuedIngestClient**。
* 若要將 Kusto 服務上不必要的負載降到最低，建議針對每個 Kusto 叢集的每個進程使用單一 Kusto 內嵌用戶端實例（佇列或直接）。 Kusto 內嵌用戶端執行是安全線程，而且完全可重新進入。

### <a name="ingestion-permissions"></a>內嵌許可權
* [Kusto](kusto-ingest-client-permissions.md)的內嵌許可權說明使用 Kusto 成功內嵌所需的許可權設定。內嵌套件

### <a name="kustoingest-library-reference"></a>Kusto 內嵌程式庫參考
* [Kusto：內嵌用戶端參考](kusto-ingest-client-reference.md)包含 Kusto 內嵌用戶端介面和執行的完整參考。<BR>您可以在這裡找到有關如何建立內嵌用戶端、擴充內嵌要求、管理內嵌進度等等的資訊。
* [Kusto 作業狀態](kusto-ingest-client-status.md)說明用於追蹤內嵌狀態的**KustoQueuedIngestClient**設施
* [Kusto。內嵌錯誤](kusto-ingest-client-errors.md)檔 Kusto 內嵌用戶端錯誤和例外狀況
* [Kusto 範例](kusto-ingest-client-examples.md)提供程式碼片段，示範將資料內嵌到 Kusto 的各種技術

### <a name="data-ingestion-rest-apis"></a>資料內嵌 REST Api
[沒有 Kusto 的資料](kusto-ingest-client-rest.md)內嵌說明如何使用 Kusto REST api 來執行排入佇列的 Kusto 內嵌，而不需依賴 Kusto 的內嵌程式庫。
