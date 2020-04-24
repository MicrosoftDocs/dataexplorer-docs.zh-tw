---
title: 庫斯特引入用戶端庫 - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Kusto 引入用戶端庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0fd86579f4ca6471903305ca9249b78bc03b8cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503123"
---
# <a name="kusto-ingest-client-library"></a>庫斯特引入用戶端庫

## <a name="overview"></a>概觀
Kusto.Ingest 庫是一個 .NET 4.6.2 庫,能夠將數據發送到 Kusto 服務。
Kusto.Ingest 依賴於以下庫和 SDK:

* AAD 認證的 ADAL
* Azure 儲存用戶端
* [TBD:外部依賴項的完整清單]

Kusto 引入方法由[IKustoingestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient)介面定義,並允許以同步和非同步模式從流、IDataReader、本地檔和 Azure blob 引入資料。

## <a name="ingest-client-flavors"></a>攝錄用戶端風味
從概念上講,引入用戶端有兩種基本風格:排隊和直接。

### <a name="queued-ingestion"></a>排隊攝取
此模式由[IKustoQueuedddclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)定義,限制對 Kusto 服務的用戶端代碼依賴項。 通過將 Kusto 引入消息發佈到 Azure 佇列來執行引入,然後從 Kusto 資料管理(引入)服務獲取該消息。 任何中間存儲專案將由收錄用戶端使用 Kusto 資料管理服務分配的資源創建。

**排隊模式的優點**包括(但不限於):

* 資料引入過程與 Kusto 發動機服務分離
* 允許在 Kusto 引擎(或引入)服務無法使用時保留引入請求
* 允許通過引入服務對入站數據進行高效且可控的聚合,從而提高性能
* 允許 Kusto 引入服務管理 Kusto 發動機服務上的引入負載
* Kusto 引入服務將根據需要在瞬態引入失敗(例如,XStore 限制)上重試
* 提供一個方便的機制來追蹤每個引入要求的進度和結果

下圖概述了與 Kusto 的佇列引入用戶端互動:

![alt 文字](../images/queued-ingest.jpg "排隊攝")

### <a name="direct-ingestion"></a>直接攝入
此模式由 IKustoDirectingestClient 定義,強制與 Kusto 引擎服務直接互動。 在此模式下,Kusto 引入服務不會調節或管理數據。 直接模式下的每個引入請求最終都會轉換為直接在 Kusto 引擎服務`.ingest`上執行的命令。
下圖概述了與 Kusto 的直接引入用戶端互動:

![alt 文字](../images/direct-ingest.jpg "直接攝")

> [!NOTE]
> 不建議生產級引入解決方案採用直接模式。

**直接模式的優點**包括:

* 低延遲(沒有聚合)。 但是,通過排隊引入也可以實現低延遲
* 使用同步方法時,方法完成指示引入操作的結束

**直接模式的缺點**包括:

* 用戶端代碼必須實現任何重試或錯誤處理邏輯
* 當 Kusto 發動機服務不可用時,無法引入
* 用戶端代碼可能會透過引入請求使 Kusto 引擎服務不堪重負,因為它不知道引擎服務容量

## <a name="ingestion-best-practices"></a>攝入最佳實踐

### <a name="general"></a>一般
[攝入最佳實踐](kusto-ingest-best-practices.md)在攝入時提供 COG 和輸送量 POV。

### <a name="thread-safety"></a>執行緒安全
Kusto 引入客戶端實現是線程安全的,旨在重用。 無需為每個`KustoQueuedIngestClient`類甚至多個引入操作創建類實例。 每個使用者進程的目標`KustoQueuedIngestClient`Kusto 群集都需要單個實例。 運行多個實例會產生反效果,並可能處理數據管理群集。

### <a name="supported-data-formats"></a>支援的資料格式
使用本機引入時(如果尚未存在),請將數據上載到一個或多個 Azure 存儲 Blob。 目前支援的 blob 格式記錄在[「支持的資料格式」](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)主題中。

### <a name="schema-mapping"></a>結構描述對應
[架構映射](../../management/mappings.md)有助於確定將源資料欄位綁定到目標表列。

## <a name="usage-and-further-reading"></a>使用與進一步閱讀

* 如上所述,為 Kusto 提供永續和大規模引入解決方案的建議基礎應該是**KustoQueueingestClient**。
* 為了最大程度地減少 Kusto 服務不必要的負載,建議每個 Kusto 群集每個行程使用一個 Kusto Ingest 用戶端(Queued 或 Direct)實例。 Kusto Ingest 客戶端實現是線程安全且完全重入的。

### <a name="ingestion-permissions"></a>引入權限
* [Kusto 引入權限](kusto-ingest-client-permissions.md)解釋了使用 Kusto.Ingest 套件成功引入所需的權限設定

### <a name="kustoingest-library-reference"></a>庫斯特.英格特庫參考
* [Kusto.Ingest 用戶端引用](kusto-ingest-client-reference.md)包含庫斯特引入用戶端介面和實現的完整引用。<BR>在那裡,您將找到有關如何創建引入客戶端、增強引入請求、管理引入進度等的資訊
* [Kusto.Ingest 操作狀態](kusto-ingest-client-status.md)解譯用於追蹤引入狀態的**KustoQueuedd 客戶端**
* [Kusto.Ingest 錯誤](kusto-ingest-client-errors.md)紀錄 Kusto 引入客戶端錯誤和異常
* [Kusto.Ingest 範例](kusto-ingest-client-examples.md)提供程式碼段,簡報了將資料引入 Kusto 的各種技術

### <a name="data-ingestion-rest-apis"></a>資料引入 REST API
[沒有 Kusto.Ingest 庫的資料引入](kusto-ingest-client-rest.md)解釋了如何使用 Kusto REST API 實現排隊 Kusto 引入,而不依賴於 Kusto.Ingest 庫。

