---
title: 使用單鍵內嵌，將資料從事件中樞內嵌至 Azure 資料總管。
description: 在本文中，您將瞭解如何使用單鍵體驗，從事件中樞將) 資料內嵌 (載入至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 6f7b33941ca38f80249808d14514faa2378c61fb
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94715011"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-data-explorer"></a>使用單鍵內嵌來建立 Azure 資料總管的事件中樞資料連線

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-hub.md)
> * [單鍵](one-click-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager 範本](data-connection-event-hub-resource-manager.md)

Azure 資料總管可從事件中樞、巨量資料串流平台及事件內嵌服務進行內嵌 (載入資料)。 [事件中樞](/azure/event-hubs/event-hubs-about)可以近乎即時地每秒鐘處理數百萬個事件。 在本文中，您會使用單鍵內嵌體驗，將事件中樞連線至 Azure 資料總管 [中的資料表](ingest-data-one-click.md) 。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 叢集[和資料庫](create-cluster-database-portal.md)。
* [具有內嵌資料的事件中樞](ingest-data-event-hub.md#create-an-event-hub)。

## <a name="ingest-new-data"></a>內嵌新資料

1. 在 [WEB UI](https://dataexplorer.azure.com/)的左側功能表中，以滑鼠右鍵按一下 *資料庫* 或 *資料表* ，然後選取 [內嵌 **新資料**]。 

:::image type="content" source="media/one-click-event-hub/one-click-ingestion-in-web-ui.png" alt-text="在 Web UI 中選取單鍵擷取":::

[內嵌 **新資料** ] 視窗隨即開啟，並選取 [ **來源** ] 索引標籤。

:::image type="content" source="media/one-click-event-hub/reference-to-event-hub.png" alt-text="將新資料內嵌至 Azure 資料總管中的 [來源] 索引標籤的螢幕擷取畫面，其中來源 = 事件中樞的參考":::

1. 資料庫 **欄位會** 自動填入您的資料庫。 您可以從下拉式功能表中選取不同的資料庫。

1. 在 [ **資料表**] 下，選取 [ **建立新** 的]，並輸入新資料表的名稱，或使用現有的資料表。 

    > [!NOTE]
    > 資料表名稱必須介於 1 到 1024 個字元之間。 您可使用英數位元、連字號和底線。 但不支援萬用字元。

1. 在 [ **來源類型**] 下，選取 [ **事件中樞的參考**]。 將會顯示 [資料連線] 選取專案。

## <a name="data-connection"></a>資料連線

1. 在 [ **資料連線**] 底下，填寫下欄欄位：

    :::image type="content" source="media/one-click-event-hub/project-details.png" alt-text="[來源] 索引標籤的螢幕擷取畫面，其中包含要填入的專案詳細資料欄位-只需要按一下即可將新資料內嵌至 Azure 資料總管使用事件中樞":::

    |**設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 資料連線名稱 | *TestDataConnection*  | 識別資料連線的名稱。
    | 訂用帳戶 |      | 事件中樞資源所在的訂用帳戶識別碼。  |
    | 事件中樞命名空間 |  | 識別命名空間的名稱。 |
    | 事件中樞 |  | 您要使用的事件中樞。 |
    | 取用者群組 |  | 在事件中樞中定義的取用者群組。 |
    | 資料格式 | | 會以 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 物件的形式從事件中樞讀取資料。 支援的格式為 CSV、JSON、PSV、SCsv、SOHsv TSV 和 TSVE。 |
    | 事件系統屬性 | 選取相關屬性 | [事件中樞系統屬性](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations)。 如果每個事件訊息有多筆記錄，系統屬性將會新增至第一個。 新增系統屬性時，請 [建立](kusto/management/create-table-command.md) 或 [更新](kusto/management/alter-table-command.md) 資料表架構和 [對應](kusto/management/mappings.md) 以包含選取的屬性。 |

1. 選取 [ **編輯架構**]。

## <a name="schema-tab"></a>結構描述索引標籤

如需使用 JSON 格式資料之架構對應的詳細資訊，請參閱 [編輯架構](one-click-ingestion-existing-table.md#edit-the-schema)。
如需有關使用 CSV 格式化資料之架構對應的詳細資訊，請參閱 [編輯架構](one-click-ingestion-new-table.md#edit-the-schema)。

:::image type="content" source="media/one-click-event-hub/schema-tab.png" alt-text="按一下體驗中的 [架構] 索引標籤的螢幕擷取畫面，其中的 [事件中樞] 會將新資料內嵌至 Azure 資料總管":::

1. 如果您在 [預覽] 視窗中看到的資料未完成，您可能需要更多資料來建立包含所有必要資料欄位的資料表。 使用下列命令從事件中樞提取新的資料：
    * **捨棄和提取新的資料**：捨棄所呈現的資料，並搜尋新的事件。
    * **提取更多資料**：除了已找到的事件之外，還會搜尋更多事件。 
    
    > [!NOTE]
    > 若要查看資料的預覽，您的事件中樞必須傳送事件。
        
1. 選取 [ **開始** 內嵌]。

## <a name="complete-data-ingestion"></a>完成資料擷取

在 [ **資料內嵌已完成** ] 視窗中，當資料內嵌順利完成時，所有步驟都會標示綠色核取記號。 下列這些步驟可讓您選擇使用 **快速查詢** 來探索資料、復原使用 **工具** 所做的變更，或 **監視** 事件中樞連線和資料。

:::image type="content" source="media/one-click-event-hub/data-ingestion-completed.png" alt-text="從事件中樞內嵌至 Azure 資料總管的最後畫面螢幕擷取畫面，其中只需要按一下即可體驗":::

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管 Web UI 中查詢資料](web-query-data.md)
* [使用 Kusto 查詢語言撰寫 Azure 資料總管的查詢](write-queries.md)
