---
title: 針對資料連線所使用的資源（例如事件中樞和 Azure 儲存體）建立私人或服務端點
description: 瞭解如何啟用私人或服務端點，以使用資料連線所使用的資源，例如事件中樞和儲存體
author: orspod
ms.author: orspodek
ms.reviewer: gunjand
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: a8e351dc04b77a41dd7ab793581a1f464f181f4e
ms.sourcegitcommit: 724d3a3c817867b17a5a5853b6433818cbc97cf7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97050513"
---
# <a name="create-a-private-or-service-endpoint-to-event-hub-and-azure-storage"></a>建立私人或服務端點至事件中樞和 Azure 儲存體

[Azure 虛擬網路 (VNet) ](/azure/virtual-network/virtual-networks-overview) 可讓許多類型的 azure 資源安全地彼此通訊。 [Azure Private Link](/azure/private-link/) 可讓您透過虛擬網路中的私人端點，來存取 azure 服務和 azure 託管的客戶擁有/合作夥伴服務。 [私人端點](/azure/private-link/private-endpoint-overview)會使用 VNet 位址空間中的 IP 位址，讓 azure 服務安全地在 azure 資料總管與 azure 服務（例如 Azure 儲存體和事件中樞）之間進行連線。 Azure 資料總管會透過 Microsoft 骨幹存取儲存體帳戶或事件中樞的私人端點，而且所有通訊（例如，資料匯出、外部資料表和資料內嵌）都會透過私人 IP 位址進行。 

相對於私人端點， [服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview) 會維持可公開路由傳送的 IP 位址。  (VNet) 服務端點的虛擬網路，可透過 Azure 骨幹網路上的優化路由，為 Azure 服務提供安全且直接的連線能力。 

本文說明如何建立 Azure 資料總管與 [事件中樞](ingest-data-event-hub-overview.md) 或 [Azure 儲存體](/azure/storage/)之間的連線。

## <a name="prerequisites"></a>必要條件

* [Azure 虛擬網路](/azure/virtual-network/virtual-networks-overview)
* [Azure 資料總管子網](vnet-deployment.md)

## <a name="private-endpoint"></a>私人端點

Azure [私人端點](/azure/private-link/private-endpoint-overview) 是一種網路介面，可讓您以私人且安全的方式連線到 Azure [Private Link](/azure/private-link/)支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 
 
# <a name="azure-storage---private-endpoint"></a>[Azure 儲存體私人端點](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>允許從使用私人端點的 Azure 資料總管子網存取 Azure 儲存體帳戶

如需如何在您的 Azure 儲存體帳戶中建立私人端點的教學課程，請參閱 [教學課程：使用 Azure 私人端點連線到儲存體帳戶](/azure/private-link/tutorial-private-endpoint-storage-portal)。

在本教學課程中，選取 Azure 資料總管子網所在的 VNet，以及 Azure 資料總管子網。

# <a name="event-hub---private-endpoint"></a>[事件中樞-私人端點](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>使用私人端點允許從 Azure 資料總管子網存取 Azure 事件中樞

如需如何在事件中樞內建立私人端點的教學課程，請參閱 [使用 Azure 入口網站新增私人端點](/azure/event-hubs/private-link-service#add-a-private-endpoint-using-azure-portal)。

在本教學課程中，選取 Azure 資料總管子網所在的 VNet，以及 Azure 資料總管子網。

---

## <a name="service-endpoint"></a>服務端點

虛擬網路 (VNet) [服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview) 可透過 azure 骨幹網路上的優化路由，為 azure 服務提供安全且直接的連線能力。 端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 

# <a name="azure-storage---service-endpoint"></a>[Azure 儲存體-服務端點](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>允許使用服務端點從 Azure 資料總管子網存取 Azure 儲存體帳戶

本節說明如何使用 Azure 入口網站來新增虛擬網路服務端點。 若要限制存取，請整合此 Azure 儲存體帳戶的虛擬網路服務端點。

### <a name="add-a-virtual-network"></a>新增虛擬網路 

1. 巡覽至您要保護的儲存體帳戶。
1. 在左側功能表中，選取 [ **防火牆和虛擬網路**]。
1. 允許從 **選取的網路** 進行存取。
1. 在 [ **虛擬網路**] 下，選取 [ **+ 新增現有的虛擬網路**]。 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-existing-vnet.png" alt-text="將現有的 VNet 連線 Azure 儲存體新增至 Azure 資料總管":::

### <a name="add-networks-pane"></a>[新增網路] 窗格

:::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-networks.png" alt-text="將虛擬網路新增至 Azure 儲存體帳戶以連接到 Azure 資料總管":::

1. 在右手邊的 [ **新增網路** ] 窗格中，選取您的 Azure 訂用帳戶。

1. 從虛擬網路清單中選取虛擬網路，然後挑選子網。 

    > [!NOTE]
    > 將虛擬網路新增至清單之前，請先啟用服務端點。 如果未啟用服務端點，入口網站會提示您啟用。
    
1. 選取 [新增]。

### <a name="save-and-verify-virtual-network-settings"></a>儲存並確認虛擬網路設定

1. 選取工具列上的 [儲存] 來儲存設定。 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-virtual-network.png" alt-text="將儲存體帳戶連線至 Azure 資料總管的 Vnet":::

    等候幾分鐘讓確認出現在入口網站通知中。

# <a name="event-hub---service-endpoint"></a>[事件中樞-服務端點](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>允許使用服務端點從 Azure 資料總管子網存取 Azure 事件中樞

> [!IMPORTANT]
> **標準** 層和 **專用** 層的事件中樞支援虛擬網路，基本層中不支援虛擬網路。 

### <a name="add-a-virtual-network"></a>新增虛擬網路

1. 在 Azure 入口網站中，流覽至您想要保護的 **事件中樞命名空間** 。
1. 在左側功能表中，選取 [ **網路**]。 此索引標籤只會出現在 **標準** 或 **專屬** 命名空間中。
1. 選取 [ **防火牆和虛擬網路** ] 索引標籤。 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/networking.png" alt-text="事件中樞中的網路功能":::

1. 允許從 **選取的網路** 進行存取。
1. 在 [ **虛擬網路**] 下，選取 [ **+ 新增現有的虛擬網路**]。 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-add-existing-vnet.png" alt-text="在 Azure 資料總管中新增現有的虛擬網路":::

### <a name="add-networks-pane"></a>[新增網路] 窗格

:::image type="content" source="media/vnet-private-link-storage-event-hub/add-networks.png" alt-text="新增網路欄位以將 VNet 連線至 Azure 資料總管":::  

1. 在右手邊的 [ **新增網路** ] 窗格中，選取您的 Azure 訂用帳戶。

1. 從虛擬網路清單中選取虛擬網路，然後挑選子網。 您必須先啟用服務端點，才能將虛擬網路新增至清單。 
    > [!NOTE]
    > 將虛擬網路新增至清單之前，請先啟用服務端點。 如果未啟用服務端點，入口網站會提示您啟用。
1. 選取 [新增]。

### <a name="save-and-verify-virtual-network-settings"></a>儲存並確認虛擬網路設定

1. 選取工具列上的 [儲存] 來儲存設定。 等候幾分鐘讓確認出現在入口網站通知上
    
    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-firewalls-and-vnet.png" alt-text="在事件中樞新增虛擬網路和子網以連接到 Azure 資料總管"::: 

---

## <a name="next-steps"></a>後續步驟

* [將資料匯出至儲存體](kusto/management/data-export/export-data-to-storage.md)
* [將資料從事件中樞內嵌至 Azure 資料總管](ingest-data-event-hub.md)
