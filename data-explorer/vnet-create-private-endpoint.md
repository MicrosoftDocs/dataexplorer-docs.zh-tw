---
title: 在您的虛擬網路中使用 Azure 資料總管叢集中的私人端點
description: 在您的虛擬網路中使用 Azure 資料總管叢集中的私人端點
author: orspod
ms.author: orspodek
ms.reviewer: elbirnbo
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: aeb807db9b69c6c5b806a7f4b152330ea2dabc72
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680745"
---
# <a name="create-a-private-endpoint-in-your-azure-data-explorer-cluster-in-your-virtual-network-preview"></a>在您的虛擬網路 (預覽版中，在 Azure 資料總管叢集中建立私人端點) 

搭配私人端點使用 Private Link，以安全地存取虛擬網路中的 Azure 資料總管叢集 (VNet) 。 

若要設定您的 [Private Link 服務](https://docs.microsoft.com/azure/private-link/private-link-service-overview)，請使用私人端點與 Azure VNet 位址空間中的 IP 位址。 [Azure 私人端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 會使用您 VNet 中的私人 IP 位址，以私人且安全的方式將您連線到 Azure 資料總管。 您也需要重新設定叢集上的 [DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-dns) 設定，以使用您的私人端點進行連接。 透過此設定，您私人網路上的用戶端與 Azure 資料總管叢集之間的網路流量會透過 VNet 和 Microsoft 骨幹網路上的 [Private Link](https://docs.microsoft.com/azure/private-link/) 來傳送，以移除公用網際網路的暴露程度。 本文說明如何在叢集中建立和設定查詢 (引擎) 和內嵌 (資料管理) 的私人端點。

## <a name="prerequisites"></a>先決條件

* [在您的虛擬網路中建立 Azure 資料總管](https://docs.microsoft.com/azure/data-explorer/vnet-create-cluster-portal)叢集
* 停用網路原則：
  * 在 Azure 資料總管叢集虛擬網路中，停用 [Private Link 服務原則](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)。
  * 在私人端點虛擬網路（可與 Azure 資料總管叢集虛擬網路相同）中，停用 [私人端點原則](https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy)。

## <a name="create-private-link-service"></a>建立 Private Link 服務

若要安全地連結到叢集上的所有服務，您需要建立 [Private Link 服務](https://docs.microsoft.com/azure/private-link/private-link-service-overview) 兩次：一次用於查詢 (引擎) ，以及一次用於內嵌 (資料管理) 。

1. 選取入口網站左上角的 [+ 建立資源]  按鈕。
1. 搜尋 *Private Link 服務*。
1. 在 [ **Private Link 服務**] 下，選取 [ **建立**]。

    :::image type="content" source="media/vnet-create-private-endpoint/create-service.gif" alt-text="Gif 顯示在 Azure 資料總管入口網站中建立 private link 服務的前三個步驟":::

1. 在 [ **建立私人連結服務** ] 窗格中，填寫下欄欄位：

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-basics.png" alt-text="建立私人連結服務中的 Tab 鍵 1-基本":::

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取保存您的虛擬網路叢集的 Azure 訂用帳戶。|
    | 資源群組 | 您的資源群組 | 選取保存虛擬網路叢集的資源群組。 |
    | 名稱 | AzureDataExplorerPLS | 選擇在資源群組中識別私用連結服務的名稱。 |
    | 區域 | 與虛擬網路相同 | 選取符合您虛擬網路區域的區域。 |

1. 在 [ **輸出設定** ] 窗格中，填寫下欄欄位：

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-outbound.png" alt-text="Private link tab 2-輸出設定":::

    |**設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 負載平衡器 | 您的引擎或 *資料管理* Load Balancer | 選取為您的叢集引擎建立的負載平衡器、負載平衡器，以指向您的引擎公用 IP。  負載平衡器引擎名稱會採用下列格式： kucompute-{clustername}-elb <br> *Load Balancer 的資料管理名稱將採用下列格式： kudatamgmt-{clustername}-elb*|
    | 負載平衡器前端 IP 位址 | 您的引擎或資料管理公用 IP。 | 選取 Load Balancer 的公用 IP 位址。 |
    | 來源 NAT 子網路 | 叢集的子網 | 部署叢集的子網。
    
1. 在 [ **存取安全性** ] 窗格中，選擇可要求存取您私用連結服務的使用者。
1. 選取 [ **審核 + 建立** ]，以檢查您的私人連結服務設定。 選取 [ **建立** ] 以建立 private link 服務。
1. 建立私人連結服務之後，請開啟資源並儲存私人連結別名，以供下一個步驟 **建立私人端點**。 範例別名是： *AzureDataExplorerPLS 111-222-333. westus. privatelinkservice*

## <a name="create-private-endpoint"></a>建立私人端點

若要安全地連結到叢集上的所有服務，您必須建立 [私人端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 兩次：一次用於查詢 (引擎) ，一次用於內嵌 (資料管理) 。

1. 選取入口網站左上角的 [+ 建立資源]  按鈕。
1. 搜尋 *私用端點*。
1. 在 [ **私人端點**] 底下，選取 [ **建立**]。
1. 在 [ **建立私人端點** ] 窗格中，填寫下欄欄位：

    :::image type="content" source="media/vnet-create-private-endpoint/step-one-basics.png" alt-text="建立私人端點表單步驟 1-基本":::

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於私人端點的 Azure 訂用帳戶。|
    | 資源群組 | 您的資源群組 | 使用現有資源群組，或建立新的資源群組。 |
    | 名稱 | AzureDataExplorerPE | 選擇可識別資源群組中虛擬網路的名稱。
    | 區域 | *美國西部* | 選取最符合您需求的區域。
    
1. 在 [ **資源** ] 窗格中，填寫下欄欄位：

    :::image type="content" source="media/vnet-create-private-endpoint/step-two-resource.png" alt-text="建立虛擬網路表單步驟 2-資源":::

    **設定** | **值**
    |---|---|
    | 連線方法 | 您 Private Link 服務別名 |
    | 資源識別碼或別名 | 您的引擎 Private Link 服務別名。 範例別名是： *AzureDataExplorerPLS 111-222-333. westus. privatelinkservice*  |
    
1. 選取 [ **審核 + 建立** ] 以查看您的私人端點設定，並 **建立** 私人端點服務。
1. 若要建立內嵌 (資料管理) 的私用端點，請遵循下列變更的相同指示：
    1. 在 [ **資源** ] 窗格中，選擇您的內嵌 (資料管理) Private Link 服務別名。

> [!NOTE]
> 您可以從多個私人端點連接到 Private Link 服務。

## <a name="approve-your-private-endpoint"></a>核准私人端點

> [!NOTE]
> 如果您在建立 Private Link 服務時，在 [**存取安全性**] 窗格中選擇 [*自動核准*] 選項，則不需要執行此步驟。

1. 在 Private Link 服務中，選擇 [設定] 下的 [ **私人端點** 連線]。
1. 從 [連接] 清單中選擇您的私人端點，然後選取 [ **核准**]。

:::image type="content" source="media/vnet-create-private-endpoint/private-link-approve.png" alt-text="建立私人端點的核准步驟"::: 

## <a name="set-dns-configuration"></a>設定 DNS 設定

當您在虛擬網路中部署 Azure 資料總管叢集時，我們會更新 [DNS 專案](https://docs.microsoft.com/azure/private-link/private-endpoint-dns) ，以指向 *privatelink* 記錄名稱和區域主機名稱之間的正式名稱。 針對引擎和內嵌 (資料管理) ，會更新此專案。 

例如，如果您的引擎 DNS 名稱是 myadx.westus.kusto.windows.net，則名稱解析將會是：

* **名稱**： myadx.westus.kusto.windows.net   <br> **類型**： CNAME   <br> **值**： myadx.privatelink.westus.kusto.windows.net
* **名稱**： myadx.privatelink.westus.kusto.windows.net   <br> **類型**： A   <br> **值**：40.122.110.154
    > [!NOTE]
    > 此值是您在建立叢集時所提供的查詢 (引擎) 公用 IP 位址。

設定私人 DNS 伺服器或 Azure 私人 DNS 區域。 針對測試，您可以修改測試電腦的主項目目。

建立下列 DNS 區域： **privatelink.region.kusto.windows.net**。 範例中的 DNS 區域是： *privatelink.westus.kusto.windows.net*。 使用 A 記錄和私人端點 IP 註冊引擎的記錄。

例如，名稱解析將會是：

* **名稱**： myadx.westus.kusto.windows.net   <br>**類型** ： CNAME   <br>**值**： myadx.privatelink.westus.kusto.windows.net
* **名稱**： myadx.privatelink.westus.kusto.windows.net   <br>**類型**： A   <br>**值**：10.3.0。9
    > [!NOTE]
    > 此值是您的私人端點 IP。 您已經將 IP 連接到查詢 (引擎) private link 服務。

設定此 DNS 設定之後，您可以使用下列 URL，在虛擬網路內以私人方式連線到查詢 (引擎) ： myadx.region.kusto.windows.net。

若要私下 (資料管理) 的內嵌，請使用 A 記錄和內嵌私人端點 IP 來註冊內嵌 (資料管理) 的記錄。

## <a name="next-steps"></a>後續步驟

* [將 Azure 資料總管叢集部署到您的虛擬網路](vnet-deployment.md)
* [針對您的虛擬網路中的 Azure 資料總管叢集存取、內嵌和操作進行疑難排解](vnet-deploy-troubleshoot.md)
