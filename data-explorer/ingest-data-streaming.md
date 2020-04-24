---
title: 使用串流式引入資料引入 Azure 資料資源管理員
description: 瞭解如何使用流式引入將數據引入(載入)到 Azure 資料資源管理器中。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 219a9014b120e0df74f8d9d286253fa933c8f05a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499614"
---
# <a name="streaming-ingestion-preview"></a>串流式引入(預覽)

當您需要低延遲且攝入時間小於 10 秒時,對於不同的卷數據,請使用流式處理。 它用於優化許多表的操作處理,在一個或多個資料庫中,其中數據流到每個表相對較小(每秒記錄很少),但總體數據引入量很高(每秒數千條記錄)。 

當數據量增長到每表超過每秒 1 MB 時,請使用批量引入,而不是流式處理。 閱讀[數據引入概述](/azure/data-explorer/ingest-data-overview),瞭解有關各種攝入方法的詳細資訊。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登入 Web [UI](https://dataexplorer.azure.com/)。
* [建立 Azure 資料資源管理器叢集和資料庫](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>在叢集上啟用串流式處理

> [!WARNING]
> 在啟用蒸汽攝入之前,請查看[限制](#limitations)。

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 在 **「設定」** 中,選擇 **「設定**」 。。 
1. 在 **「設定」** 選單中,選擇 **「開啟**」 以開啟**流式處理**。
1. 選取 [儲存]  。
 
    ![流引入](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. 在[Web UI](https://dataexplorer.azure.com/)中,在接收串流資料的表或資料庫上定義[串流引入策略](kusto/management/streamingingestionpolicy.md)。 

    > [!NOTE]
    > * 如果在資料庫級別定義了策略,則資料庫中的所有表都啟用以進行流式處理。
    > * 應用的策略只能引用新引入的數據,而不能引用資料庫中的其他表。

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>使用串流式引入將資料引入叢集

有兩種受支援的流式處理類型:


* [**事件中心**](/azure/data-explorer/ingest-data-event-hub), 用作資料來源
* **自定義引入**要求您編寫使用 Azure 資料資源管理員用戶端庫之一的應用程式。 請參考[範例應用程式的串流式引入範例](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>選擇適當的串流式引入類型

|   |事件中樞  |自訂引入  |
|---------|---------|---------|
|引入啟動及可用於查詢的資料之間的資料延遲   |    更長的延遲     |   更短的延遲      |
|開發花費    |   快速、簡便的設定,無需開發開銷    |   應用程式處理錯誤並確保資料一致性的高開發花費     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>關閉叢集上的串流式

> [!WARNING]
> 禁用流式處理可能需要幾個小時。

1. 從所有相依表和資料庫中移除[串流式引入策略](kusto/management/streamingingestionpolicy.md)。 流式引入策略刪除觸發從初始存儲到列存儲中的永久存儲(範圍或分片)的流式引入數據移動。 數據移動可以持續幾秒鐘到幾個小時,具體取決於初始存儲中的數據量以及群集使用 CPU 和記憶體的方式。
1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 在 **「設定」** 中,選擇 **「設定**」 。。 
1. 在 **「設定」** 選單中,選擇 **「關閉**」 以關閉**的流式處理**。
1. 選取 [儲存]  。

    ![串流式傳送](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>限制

* 串流式引入不支援[資料庫游標](kusto/management/databasecursor.md)或[資料映射](kusto/management/mappings.md)。 僅支援[預先創建](kusto/management/create-ingestion-mapping-command.md)的數據映射。 
* 流式處理性能和容量隨 VM 和群集大小增加而擴展。 併發引入僅限於每個核心六個引入。 例如,對於 16 個核心 SKU(如 D14 和 L16),最大支援的負載為 96 個併發引入。 對於兩個核心 SKU(如 D11),最大支援的負載為 12 個併發引入。
* 每個引入請求的數據大小限制為 4 MB。
* 架構更新(如創建和修改表和引入映射)可能需要長達五分鐘的時間進行流式處理服務。
* 在群集上啟用流式處理(即使資料不是通過流引入)也會使用群集計算機的本地 SSD 磁碟的一部分來流式傳輸引入數據,並減少可用於熱緩存的儲存。
* 無法在串流式處理資料上設定[延伸磁碟區標記](kusto/management/extents-overview.md#extent-tagging)。

## <a name="next-steps"></a>後續步驟

* [Azure 資料資源管理員中的查詢資料](web-query-data.md)
