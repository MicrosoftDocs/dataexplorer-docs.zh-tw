---
title: 在您的虛擬網路中建立 Azure 資料總管叢集 & DB
description: 在本文中，您將瞭解如何在虛擬網路中建立 Azure 資料總管叢集。
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 097e175ff28d334532e85715f1f6401a96fa8f8c
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374320"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>在您的虛擬網路中建立 Azure 資料總管叢集

Azure 資料總管支援將叢集部署到虛擬網路（VNet）中的子網。 這項功能可讓您從 Azure 虛擬網路或內部部署私下存取叢集、存取資源（例如事件中樞和虛擬網路內的儲存體），以及限制輸入和輸出流量。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-network-security-group-nsg"></a>建立網路安全性群組（NSG）

[網路安全性群組（NSG）](/azure/virtual-network/security-overview)提供控制 VNet 內網路存取的能力。 您可以使用兩個端點 HTTPs （443）和 TDS （1433）來存取 Azure 資料總管。 下列 NSG 規則必須設定為允許存取這些端點，以進行叢集的管理、監視和適當操作。

若要建立網路安全性群組：

1. 選取入口網站左上角的 [ **+ 建立資源**] 按鈕。
1. 搜尋 [*網路安全性群組*]。
1. 在 [**網路安全性群組**] 底下，選取畫面底部的 [**建立**]。
1. 在 [**建立網路安全性群組**] 視窗中，填寫下列資訊。

   ![建立 NSG 表單](media/vnet-create-cluster-portal/network-security-group.png)

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於叢集的 Azure 訂用帳戶。|
    | 資源群組 | 您的資源群組 | 使用現有資源群組，或建立新的資源群組。 |
    | 名稱 | AzureDataExplorerNsg | 選擇名稱，以識別資源群組中的網路安全性群組（NSG）。  |
    | 區域 | *美國西部* | 選取最符合您需求的區域。
    | | | |

1. 選取 [檢閱 + 建立]**** 以檢閱您的叢集詳細資料，然後選取 [建立]**** 以佈建叢集。

1. 部署完成後，請選取 [移至資源]****。

    ![前往資源](media/vnet-create-cluster-portal/notification-nsg.png)

1. 在 [**輸入安全性規則**] 索引標籤中，選取 [ **+ 新增**]。
1. 在 [**新增輸入安全性規則**] 視窗中，填寫下列資訊。

    ![建立 NSG 輸入規則表單](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **設定** | **建議的值** 
    |---|---|
    | 來源 | ServiceTag
    | 來源服務標籤 | AzureDataExplorerManagement
    | 來源連接埠範圍 | *
    | Destination | VirtualNetwork
    | 目的地連接埠範圍 | *
    | 通訊協定 | TCP
    | 動作 | Allow
    | 優先順序 | 100
    | 名稱 | AllowAzureDataExplorerManagement
    | | |
    
1. 根據[VNet 部署](vnet-deployment.md#dependencies-for-vnet-deployment)的相依性，針對所有輸入和輸出相依性重複前兩個步驟。 或者，您可以使用單一規則來取代輸出規則，以允許埠443和80的*網際網路*。
    
    輸入和輸出相依性的 NSG 規則看起來應該像這樣：

    ![NSG 規則](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>建立公用 IP 位址

若要建立查詢（引擎）公用 IP 位址：

1. 選取入口網站左上角的 [ **+ 建立資源**] 按鈕。
1. 搜尋 [*公用 IP 位址*]。
1. 在 [公用 IP 位址]**** 下方，選取 [建立]****。
1. 在 [**建立公用 IP 位址**] 窗格中，完成下列資訊。
   
   ![建立公用 IP 表單](media/vnet-create-cluster-portal/public-ip-blade.png)

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | IP 版本 | IPv4 | 選取 IP 版本。 我們只支援 IPv4。|
    | SKU | 標準 | 我們需要**標準**for Query （引擎） URI 端點。 |
    | 名稱 | 引擎-pip | 選擇用來識別資源群組中公用 IP 位址的名稱。
    | 訂用帳戶 | 您的訂用帳戶 | 選取您想要用於公用 IP 的 Azure 訂用帳戶。|
    | 資源群組 | 您的資源群組 | 使用現有資源群組，或建立新的資源群組。 |
    | Location | *美國西部* | 選取最符合您需求的區域。
    | | | |

1. 選取 [**建立**] 以建立公用 IP 位址。

1. 若要建立內嵌（資料管理）公用 IP 位址，請遵循相同的指示，然後選取 
    * **Sku**：基本
    * **IP 位址指派**：靜態

## <a name="create-virtual-network-and-subnet"></a>建立虛擬網路和子網

若要建立虛擬網路和子網：

1. 選取入口網站左上角的 [ **+ 建立資源**] 按鈕。
1. 搜尋*虛擬網路*。
1. 在 [**虛擬網路**] 底下，選取畫面底部的 [**建立**]。
1. 在 [**建立虛擬網路**] 視窗中，完成下列資訊。

   ![建立虛擬網路表單](media/vnet-create-cluster-portal/vnet-blade.png)

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於叢集的 Azure 訂用帳戶。|
    | 資源群組 | 您的資源群組 | 使用現有資源群組，或建立新的資源群組。 |
    | 名稱 | AzureDataExplorerVnet | 選擇用來識別資源群組中虛擬網路的名稱。
    | 區域 | *美國西部* | 選取最符合您需求的區域。
    | | | |

    > [!NOTE]
    > 針對生產工作負載，請根據[您 VNet 中的規劃子網大小](vnet-deployment.md#plan-subnet-size-in-your-vnet)來規劃子網大小

1. 選取 [檢閱 + 建立]**** 以檢閱您的叢集詳細資料，然後選取 [建立]**** 以佈建叢集。

1. 部署完成後，請選取 [移至資源]****。
1. 移至 [**子網**] 分頁，然後選取 [**預設**子網]。
    
    ![子網分頁](media/vnet-create-cluster-portal/subnets.png)

1. 在 [**預設**子網] 視窗中：
    1. 從下拉式功能表中選取您的**網路安全性群組**。 
    1. 選取您的網路安全性群組名稱，在此案例中為**AzureDataExplorerNsg**。 
    1. **儲存**

    ![設定子網](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>建立叢集

在 Azure 資源群組中建立具有一組已定義計算和儲存體資源的 Azure 資料總管叢集，如[建立](create-cluster-database-portal.md#create-a-cluster)叢集中所述。

1. 在完成叢集建立之前，請在 [**建立 Azure 資料總管**叢集] 視窗中，選取 [**網路**] 索引標籤，以使用在先前的索引標籤中建立的資源來提供虛擬網路詳細資料：

   ![建立叢集 vnet 表單](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您想要用於網路資源的 Azure 訂用帳戶。|
    | 虛擬網路 | AzureDataExplorerVnet | 選擇在先前步驟中建立的虛擬網路。
    | 子網路 | default | 選擇在先前步驟中建立的子網。
    | 查詢公用 IP | 引擎-pip | 選擇在先前步驟中建立的查詢公用 IP。
    | 資料內嵌公用 IP | dm-pip | 選擇在先前步驟中建立的內嵌公用 IP。
    | | | |

1. 選取 [**審核] + [建立**] 以建立您的叢集。
1. 部署完成後，請選取 [移至資源]****。

若要將您的 Azure 資料總管叢集部署到虛擬網路，請使用將[azure 資料總管叢集部署至您的 VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager 範本。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將 Azure 資料總管部署至您的虛擬網路](vnet-deployment.md)
