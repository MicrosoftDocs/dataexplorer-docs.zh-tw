---
title: 在您的儲存體帳戶中建立事件方格訂用帳戶-Azure 資料總管
description: 本文說明如何在 Azure 資料總管的儲存體帳戶中建立事件方格訂用帳戶。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 51d5c48fbcd2373ed5cdd1973cc9e53abb26b2de
ms.sourcegitcommit: 58588ba8d1fc5a6adebdce2b556db5bc542e38d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92099527"
---
# <a name="manually-create-resources-for-event-grid-ingestion"></a>手動建立事件方格內嵌的資源

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-grid.md)
> * [入口網站-手動建立資源](ingest-data-event-grid-manual.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 範本](data-connection-event-grid-resource-manager.md)

Azure 資料總管使用 [事件方格內嵌管線](ingest-data-event-grid-overview.md)，從 Azure 儲存體 (Azure Blob 儲存體和 Azure Data Lake Storage Gen2) 提供連續的內嵌。 在事件方格內嵌管線中，Azure 事件方格服務會透過 Azure 事件中樞將 blob 建立或 blob 重新命名事件從儲存體帳戶路由傳送到 Azure 資料總管。

在本文中，您將瞭解如何手動建立事件方格內建所需的資源：事件方格訂用帳戶、事件中樞命名空間和事件中樞。 [必要條件](#prerequisites)中描述事件中樞命名空間和事件中樞建立。 若要在定義事件方格內嵌時使用自動建立這些資源，請參閱 [在 Azure 中建立事件方格資料連線資料總管](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 建立 [免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* [儲存體帳戶](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)。
    * 您可以針對 `BlobStorage` 、 `StorageV2` 或 [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction)Azure 儲存體帳戶設定事件方格通知訂閱。
* [事件中樞命名空間和事件中樞](/azure/event-hubs/event-hubs-create)。

> [!NOTE]
> 為了達到最佳效能，請在與 Azure 資料總管叢集相同的區域中建立所有資源。

## <a name="create-an-event-grid-subscription"></a>建立事件格線訂用帳戶
 
1. 在 Azure 入口網站中，移至您的儲存體帳戶。
1. 在左側功能表中，選取 [**事件**  >  **訂閱**]。

     :::image type="content" source="media/eventgrid/create-event-grid-subscription-1.png" alt-text="建立事件方格訂用帳戶":::

1. 在 [建立事件訂用帳戶]**** 視窗的 [基本]**** 索引標籤內，提供下列值：

    :::image type="content" source="media/eventgrid/create-event-grid-subscription-2.png" alt-text="建立事件方格訂用帳戶":::

    |**設定** | **建議的值** | **欄位描述**|
    |---|---|---|
    | 名稱 | *test-grid-connection* | 您要建立之事件方格訂用帳戶的名稱。|
    | 事件結構描述 | *事件方格架構* | 應該用於 Event Grid 的結構描述。 |
    | 主題類型 | *儲存體帳戶* | 事件方格主題的類型。 自動填入。|
    | 來源資源 | *gridteststorage1* | 儲存體帳戶的名稱。 自動填入。|
    | 系統主題名稱 | *gridteststorage1...* | Azure 儲存體發佈事件的系統主題。 然後，這個系統主題會將事件轉送至接收和處理事件的訂閱者。 自動填入。|
    | 篩選至事件種類 | *已建立 Blob* | 要取得通知的特定事件。 建立訂用帳戶時，請選取目前支援的其中一種類型： BlobCreated。 或 BlobRenamed|

1. 在 [ **端點詳細資料**] 中，選取 [ **事件中樞**]。

    :::image type="content" source="media/eventgrid/endpoint-details.png" alt-text="建立事件方格訂用帳戶":::

1. 按一下 [ **選取端點** ]，然後填入您所建立的事件中樞，例如 [ *測試中樞*]。
    
1. 如果您想要追蹤特定的主題，請選取 [ **篩選** ] 索引標籤。 設定通知的篩選條件，如下所示：
   
    :::image type="content" source="media/eventgrid/filters-tab.png" alt-text="建立事件方格訂用帳戶":::

   1. 選取 [啟用主旨篩選]****
   1. 主旨**開頭**為欄位是主體的*常*值前置詞。 因為套用的模式是 *startswith*，它可以跨越多個容器、資料夾或 blob。 不允許使用萬用字元。
       * 若要在 blob 容器上定義篩選，請依照下列方式設定欄位： *`/blobServices/default/containers/[container prefix]`* 。
       * 若要在 blob 首碼 (或 Azure Data Lake Gen2) 中的資料夾上定義篩選，請依照下列方式設定欄位： *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* 。
   1. [主旨結尾為]**** 欄位是 Blob 的*常值*後置詞。 不允許使用萬用字元。
   1. 區分**大小寫的主體相符**欄位指出前置詞和後置詞篩選準則是否區分大小寫。

    如需有關篩選事件的詳細資訊，請參閱 [blob 儲存體事件](/azure/storage/blobs/storage-blob-event-overview#filtering-events)。

1. 選取 [建立] 

## <a name="next-steps"></a>後續步驟

* 繼續進行設定，並透過 Azure 入口網站： [在 azure 資料總管中建立 Event Grid 資料](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)連線，以建立與 azure 資料總管的資料內嵌連接。

* 如果您不打算繼續使用您所建立的資源來內嵌事件方格，而不想再使用資源，請 [清除資源](ingest-data-event-grid.md#clean-up-resources) 以避免產生成本。
