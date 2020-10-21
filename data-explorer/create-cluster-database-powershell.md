---
title: 使用 Powershell 建立 Azure 資料總管叢集 & DB
description: 了解如何使用 PowerShell 建立 Azure 資料總管叢集與資料庫
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: bb15bf9393ff6ae4a06fa97ade4070dfcd5e2ef8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343128"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>使用 PowerShell 建立 Azure 資料總管叢集與資料庫

> [!div class="op_single_selector"]
> * [入口網站](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM 範本](create-cluster-database-resource-manager.md)  

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 在本文中，您會使用 Powershell 來建立叢集和資料庫。 您可以使用 [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) 在 Windows 和 Linux 上或在 [Azure Cloud Shell](/azure/cloud-shell/overview) 中執行 PowerShell Cmdlet 和指令碼，以建立及設定 Azure 資料總管叢集與資料庫。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](includes/updated-for-az.md)]

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 Azure CLI，本文需要 Azure CLI 版本2.0.4 版或更新版本。 執行 `az --version` 來檢查您的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="configure-parameters"></a>設定參數

如果您在 Azure Cloud Shell 中執行命令，則不需要執行下列步驟。 如果您在本機執行 CLI，請依照步驟 1 和 2 登入 Azure 並設定您目前的訂用帳戶：

1. 執行下列命令以登入 Azure：

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. 設定要建立叢集的訂用帳戶：

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. 在本機或 Azure Cloud Shell 中執行 Azure CLI 時，您需要在裝置上安裝 Az.Kusto 模組：

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>建立 Azure 資料總管叢集

1. 使用下列命令建立您的叢集：

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | Name | *mykustocluster* | 所需的叢集名稱。|
   | SKU | *D13_v2* | 將用於叢集的 SKU。 |
   | resourceGroupName | *>testrg* | 將在其中建立叢集的資源群組名稱。 |

    有其他選擇性參數可供您使用，例如叢集的容量。

1. 執行下列命令來檢查是否已成功建立叢集：

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

如果結果中包含有 `Succeeded` 值的 `provisioningState`，表示已成功建立叢集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集中建立資料庫

1. 使用下列命令建立您的資料庫：

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | ClusterName | *mykustocluster* | 將在其中建立資料庫的叢集名稱。|
   | Name | *mykustodatabase* | 您的資料庫名稱。|
   | resourceGroupName | *>testrg* | 將在其中建立叢集的資源群組名稱。 |
   | SoftDeletePeriod | *3650:00:00:00* | 將保留資料以供查詢的時間長度。 |
   | HotCachePeriod | *3650:00:00:00* | 資料將保留在快取中的時間長度。 |

1. 執行下列命令以查看您所建立的資料庫：

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

您此時有一個叢集和一個資料庫。

## <a name="clean-up-resources"></a>清除資源

* 如果您打算遵循其他文章，請保留您建立的資源。
* 若要清除資源，請刪除叢集。 您刪除叢集時，也會刪除其中的所有資料庫。 使用下列命令刪除您的叢集：

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>後續步驟

* [其他 Az.Kusto 命令](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [使用 Azure 資料總管 .NET Standard SDK 內嵌資料 (預覽)](./net-sdk-ingest-data.md)