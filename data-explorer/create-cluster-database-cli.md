---
title: 使用 Azure CLI 建立 azure 資料資源管理器叢集&資料庫
description: 了解如何使用 Azure CLI 建立 Azure 資料總管叢集與資料庫
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 342ba4a72ec365b3b3272cb073fd950112118973
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497144"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>使用 Azure CLI 建立 Azure 資料總管叢集與資料庫

> [!div class="op_single_selector"]
> * [入口網站](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM 範本](create-cluster-database-resource-manager.md)

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 在本文中,可以使用 Azure CLI 創建群集和資料庫。

## <a name="prerequisites"></a>Prerequisites

要完成本文,您需要 Azure 訂閱。 如果沒有,請先[創建一個免費帳戶](https://azure.microsoft.com/free/)。"

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

如果選擇在本地安裝和使用 Azure CLI,則本文需要 Azure CLI 版本 2.0.4 或更高版本。 執行 `az --version` 來檢查您的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="configure-the-cli-parameters"></a>設定 CLI 參數

如果您在 Azure Cloud Shell 中執行命令，則不需要執行下列步驟。 如果您在本機執行 CLI，請依照下列步驟登入 Azure 並設定您目前的訂用帳戶：

1. 執行下列命令以登入 Azure：

    ```azurecli-interactive
    az login
    ```

1. 設定要建立叢集的訂用帳戶。 將 `MyAzureSub` 取代為您要使用的 Azure 訂用帳戶名稱：

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>建立 Azure 資料總管叢集

1. 使用下列命令建立您的叢集：

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | NAME | *azureclitest* | 所需的叢集名稱。|
   | sku | *D13_v2* | 將用於叢集的 SKU。 |
   | resource-group | *testrg* | 將在其中建立叢集的資源群組名稱。 |

    有其他選擇性參數可供您使用，例如叢集的容量。

1. 執行下列命令來檢查是否已成功建立叢集：

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

如果結果中包含有 `Succeeded` 值的 `provisioningState`，表示已成功建立叢集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集中建立資料庫

1. 使用下列命令建立您的資料庫：

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | 叢集名稱 | *azureclitest* | 將在其中建立資料庫的叢集名稱。|
   | NAME | *clidatabase* | 您的資料庫名稱。|
   | resource-group | *testrg* | 將在其中建立叢集的資源群組名稱。 |
   | soft-delete-period | *P365D* | 表示保留資料以供查詢的時間長度。 如需詳細資訊，請參閱[保留原則](kusto/management/retentionpolicy.md)。 |
   | hot-cache-period | *P31D* | 表示資料保留在快取中的時間長度。 如需詳細資訊，請參閱[快取原則](kusto/management/cachepolicy.md)。 |

1. 執行下列命令以查看您所建立的資料庫：

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

您此時有一個叢集和一個資料庫。

## <a name="clean-up-resources"></a>清除資源

* 如果您計劃關注我們的其他文章,請保留您創建的資源。
* 若要清除資源，請刪除叢集。 您刪除叢集時，也會刪除其中的所有資料庫。 使用下列命令刪除您的叢集：

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資料資源管理員 Python 函式庫引入資料](python-ingest-data.md)
