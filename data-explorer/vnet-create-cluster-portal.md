---
title: 在虛擬網路中建立 azure 資料資源管理器群集&資料庫
description: 在本文中,您將瞭解如何在虛擬網路中創建 Azure 資料資源管理器群集。
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500797"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>在虛擬網路中建立 Azure 資料資源管理器群集

Azure 資料資源管理員支援將群集部署到虛擬網路 (VNet) 中的子網。 此功能使您能夠從 Azure 虛擬網路或內部部署私下訪問群集,存取虛擬網路中的事件集線器和儲存等資源,並限制入站和出站流量。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-network-security-group-nsg"></a>建立網路安全群組 (NSG)

[網路安全組 (NSG)](/azure/virtual-network/security-overview)提供控制 VNet 內網路訪問的能力。 可以使用兩個終結點 HTT (443) 和 TDS (1433) 訪問 Azure 數據資源管理器。 必須配置以下 NSG 規則,以允許訪問這些終結點,以便管理、監視和正確操作群集。

要建立網路安全群組,請:

1. 選擇門戶左上角的 **「創建資源**」按鈕。
1. 搜尋*網路安全群組*。
1. 在**螢幕底部的網路安全組**下,選擇 **"創建**"。
1. 在 **「創建網路安全組」** 視窗中,填寫以下資訊。

   ![建立 NSG 表單](media/vnet-create-cluster-portal/network-security-group.png)

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於叢集的 Azure 訂用帳戶。|
    | 資源群組 | 您的資源群組 | 使用現有資源群組，或建立新的資源群組。 |
    | 名稱 | Azure 資料資源管理員 | 在資源組中選擇標識網路安全組 (NSG) 的名稱。  |
    | 區域 | *美國西部* | 選取最符合您需求的區域。
    | | | |

1. 選取 [檢閱 + 建立]**** 以檢閱您的叢集詳細資料，然後選取 [建立]**** 以佈建叢集。

1. 部署完成後，請選取 [移至資源]****。

    ![前往資源](media/vnet-create-cluster-portal/notification-nsg.png)

1. 在 **「入站安全規則」** 選項卡中,選擇 **「添加**」。
1. 在 **「添加入站安全規則」** 視窗中,填寫以下資訊。

    ![建立 NSG 輸入站規則表單](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **設定** | **建議的值** 
    |---|---|
    | 來源 | 服務標籤
    | 來源服務標籤 | Azure 資料資源管理員管理員管理員管理員管理員管理
    | 來源連接埠範圍 | *
    | Destination | VirtualNetwork
    | 目的地連接埠範圍 | *
    | 通訊協定 | TCP
    | 動作 | Allow
    | 優先順序 | 100
    | 名稱 | 允許 Azure 資料資源管理員管理員管理員管理員管理員管理員管理員管理員管理員管理員管理員管理
    | | |
    
1. 根據[VNet 部署的依賴項](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment),對所有入站和出站依賴項重複前兩個步驟。 或者,出站規則可以替換為單個規則,以允許埠 443 和 80 的*Internet。*
    
    入站和出站依賴項的 NSG 規則應如下所示:

    ![NSG 規則](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>建立公共 IP 位址

要建立查詢(引擎)公共 IP 位址,請處理:

1. 選擇門戶左上角的 **「創建資源**」按鈕。
1. 搜尋*網路安全群組*。
1. 在**螢幕底部的公共 IP 位址**下,選擇 **"創建**"。
1. 在 **"創建公共 IP 位址**"窗格中,完成以下資訊。
   
   ![建立公共 IP 表單](media/vnet-create-cluster-portal/public-ip-blade.png)

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | IP 版本 | IPv4 | 選擇IP版本。 我們僅支援 IPv4。|
    | SKU | 標準 | 我們需要查詢(引擎)URI 終結點**的標準**。 |
    | 名稱 | 發動機點 | 選擇在資源組中識別公共 IP 位址的名稱。
    | 訂用帳戶 | 您的訂用帳戶 | 選擇要用於公共 IP 的 Azure 訂閱。|
    | 資源群組 | 您的資源群組 | 使用現有資源群組，或建立新的資源群組。 |
    | Location | *美國西部* | 選取最符合您需求的區域。
    | | | |

1. 選擇 **「建立**」 以建立公共 IP 位址。

1. 要建立引入(資料管理)公共 IP 位址,請遵循相同的說明並選擇 
    * **SKU**: 基本
    * **IP 位址配置**: 靜態

## <a name="create-virtual-network-and-subnet"></a>建立虛擬網路和子網路

要創建虛擬網路和子網,

1. 選擇門戶左上角的 **「創建資源**」按鈕。
1. 搜尋*虛擬網路*。
1. 在**螢幕底部的虛擬網路**下,選擇 **"創建**"。
1. 在 **「創建虛擬網路**」視窗中,完成以下資訊。

   ![建立虛擬網路窗體](media/vnet-create-cluster-portal/vnet-blade.png)

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於叢集的 Azure 訂用帳戶。|
    | 資源群組 | 您的資源群組 | 使用現有資源群組，或建立新的資源群組。 |
    | 名稱 | Azure 資料資源管理員Vnet | 在資源組中選擇標識虛擬網路的名稱。
    | 區域 | *美國西部* | 選取最符合您需求的區域。
    | | | |

    > [!NOTE]
    > 對於生產工作負載,根據[VNet 中的計劃子網大小規劃子網大小](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet)

1. 選取 [檢閱 + 建立]**** 以檢閱您的叢集詳細資料，然後選取 [建立]**** 以佈建叢集。

1. 部署完成後，請選取 [移至資源]****。
1. 跳到**子網**邊欄選項卡並選擇**預設**子網。
    
    ![子網刀片](media/vnet-create-cluster-portal/subnets.png)

1. 在**預設**子網視窗中:
    1. 從下拉選單中選擇**您的網路安全群組**。 
    1. 選擇網路安全組名稱,在這種情況下 **,AzureDataExplorerNsg。** 
    1. **儲存**

    ![設定子網](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>建立叢集

創建 Azure 資料資源管理器群集,在 Azure 資源組中使用一組定義的計算和儲存資源,如[創建群集](create-cluster-database-portal.md#create-a-cluster)中所述。

1. 在完成叢集建立之前,在 **「建立 Azure 資料資源管理員」叢集**視窗中,選擇 **「網路**」選項卡,使用前面選項卡中建立的資源提供虛擬網路詳細資訊:

   ![建立叢集 vnet 窗體](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選擇要用於網路資源的 Azure 訂閱。|
    | 虛擬網路 | Azure 資料資源管理員Vnet | 選擇在前面的步驟中創建的虛擬網路。
    | 子網路 | default | 選擇在前面的步驟中創建的子網。
    | 查詢公共 IP | 發動機點 | 選擇在前面的步驟中創建的查詢公共 IP。
    | 資料引入公共 IP | dm-pip | 選擇在前面的步驟中創建的引入公共 IP。
    | | | |

1. 選擇 **「查看 + 創建」** 以建立群組。
1. 部署完成後，請選取 [移至資源]****。

要將 Azure 資料資源管理器群集部署到虛擬網路,請使用[「將 Azure 資料資源管理器」叢集部署到 VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure 資源管理器樣本中。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將 Azure 資料資源管理員部署到虛擬網路](vnet-create-cluster-portal.md)