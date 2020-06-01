---
title: 使用串流內嵌將資料內嵌至 Azure 資料總管
description: 瞭解如何使用串流內嵌，將資料內嵌（載入）至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4bbb076b4c21ac2f93b2bdaf775d513483332934
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257955"
---
# <a name="streaming-ingestion-preview"></a>串流內嵌（預覽）

當您需要低延遲且針對各種磁片區資料的內嵌時間小於10秒時，請使用串流內嵌。 它可用來在一或多個資料庫中優化許多資料表的工作處理，其中每個資料表中的資料流程相對較小（每秒少筆記錄），但整體資料內嵌磁片區為高（每秒數千筆記錄）。 

當資料量成長到每個資料表每小時 4 GB 以上時，請使用大量內嵌，而不是串流內嵌。 閱讀[資料內嵌總覽](ingest-data-overview.md)，以深入瞭解內嵌的各種方法。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登入[WEB UI](https://dataexplorer.azure.com/)。
* 建立[Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>在您的叢集中啟用串流內嵌

> [!WARNING]
> 啟用串流內嵌之前，請先檢查這些[限制](#limitations)。

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 在 [設定] 中 **，選取 [****設定**]。 
1. **在 [設定**] 窗格中，選取 [**開啟**] 以啟用**串流**內嵌。
1. 選取 [儲存]。
 
    ![串流內嵌于](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. 在[WEB UI](https://dataexplorer.azure.com/)中，定義將接收串流資料之資料表或資料庫的[串流內嵌原則](kusto/management/streamingingestionpolicy.md)。 

    > [!NOTE]
    > * 如果原則是在資料庫層級定義，則資料庫中的所有資料表都會啟用串流內嵌。
    > * 套用的原則只能參考新內嵌的資料，而不是資料庫中的其他資料表。

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>使用串流內嵌將資料內嵌至您的叢集

支援的串流內嵌類型有兩種：

* [**事件中樞**](ingest-data-event-hub.md)，用來做為資料來源。
* **自訂**內嵌需要您撰寫使用其中一個 Azure 資料總管[用戶端程式庫](kusto/api/client-libraries.md)的應用程式。 如需範例應用程式，請參閱[串流內嵌範例](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>選擇適當的串流內嵌類型

|   |事件中樞  |自訂內嵌  |
|---------|---------|---------|
|內嵌初始化與可供查詢的資料之間的資料延遲   |    較長延遲     |   延遲較短      |
|開發額外負荷    |   快速且輕鬆的設定，無需任何開發負擔    |   應用程式的高開發額外負荷，可處理錯誤並確保資料一致性     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>停用叢集上的串流內嵌

> [!WARNING]
> 停用串流內嵌可能需要幾個小時的時間。

1. 從所有相關的資料表和資料庫中卸載[串流內嵌原則](kusto/management/streamingingestionpolicy.md)。 串流的內嵌原則移除會觸發從初始儲存體到資料行存放區（範圍或分區）中的永久儲存體的串流內嵌資料移動。 視初始儲存體中的資料量，以及叢集使用 CPU 和記憶體的方式而定，資料移動可以在數秒到幾個小時之間進行。
1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 在 [設定] 中 **，選取 [****設定**]。
1. **在 [設定**] 窗格中，選取 [**關閉**] 以停用**串流**內嵌。
1. 選取 [儲存]。

    ![串流內嵌已關閉](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>限制

* 如果資料庫本身或其任何資料表已定義並啟用[串流內嵌原則](kusto/management/streamingingestionpolicy.md)，資料庫就不支援資料庫資料[指標](kusto/management/databasecursor.md)。
* 必須[預先建立](kusto/management/create-ingestion-mapping-command.md)[資料對應](kusto/management/mappings.md)，才能用於串流內嵌。 個別的串流內嵌要求不會容納內嵌資料對應。
* 透過增加的 VM 和叢集大小，串流處理內嵌的效能和容量規模。 並行的內嵌要求數目限制為每個核心六個。 例如，針對16核心 Sku （例如 D14 和 L16 也），支援的最大負載為96並行內嵌要求。 針對兩個核心 Sku （例如 D11），支援的最大負載為12個並行內嵌要求。
* 串流內嵌要求的資料大小限制為 4 MB。
* 架構更新最多可能需要五分鐘的時間，才可供串流內嵌服務使用。 這些更新的範例包括建立和修改資料表和內嵌對應。 
* 在叢集上啟用串流內嵌，即使資料不是透過串流內嵌，也會使用叢集機器的部分本機 SSD 磁片來串流內嵌資料，並減少用於熱快取的儲存體。
* 無法在串流內嵌資料上設定[範圍標記](kusto/management/extents-overview.md#extent-tagging)。

## <a name="next-steps"></a>後續步驟

* [查詢 Azure 中的資料資料總管](web-query-data.md)
