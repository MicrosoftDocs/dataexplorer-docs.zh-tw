---
title: 使用 Azure Resource Manager 範本建立 Azure 資料總管叢集和資料庫
description: 瞭解如何使用 Azure Resource Manager 範本建立 Azure 資料總管叢集和資料庫
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/26/2019
ms.openlocfilehash: 46ccf006c6f2cf167953c64bcaa2f3de0fbaf4b6
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942211"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立 Azure 資料總管叢集和資料庫

> [!div class="op_single_selector"]
> * [入口網站](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager 範本](create-cluster-database-resource-manager.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 

在本文中，您將使用 [Azure Resource Manager 範本](/azure/azure-resource-manager/management/overview)來建立 Azure 資料總管叢集和資料庫。 本文說明如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。 如需建立範本的詳細資訊，請參閱 [撰寫 Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-authoring-templates)。 如需在範本中使用的 JSON 語法和屬性，請參閱 [Kusto 資源類型](/azure/templates/microsoft.kusto/allversions)。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>用於建立叢集和資料庫的 Azure Resource Manager 範本

在本文中，您會使用 [現有的快速入門範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

若要尋找更多範本範例，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)。

## <a name="deploy-the-template-and-verify-template-deployment"></a>部署範本並確認範本部署

您可以 [使用 Azure 入口網站](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) 或 [使用 powershell](#use-powershell-to-deploy-the-template-and-verify-template-deployment)來部署 Azure Resource Manager 範本。

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>使用 Azure 入口網站部署範本並確認範本部署

1. 若要建立叢集和資料庫，請使用下列按鈕來開始部署。 按一下滑鼠右鍵並選取 [在新視窗中開啟]****，以便依照本文中的其餘步驟操作。

    [![藍色按鈕的螢幕擷取畫面，其中圖片雲端和已標示為 [部署至 Azure]。](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    [部署至 Azure]**** 按鈕可將您帶往 Azure 入口網站，填寫部署表單。

    :::image type="content" source="media/create-cluster-database-resource-manager/deploy-2-azure.png" alt-text="Azure 入口網站上範本的螢幕擷取畫面。所有用於編輯的按鈕、方塊和核取方塊都會反白顯示。" border="false":::

    您可以使用表單， [在 Azure 入口網站中編輯和部署範本](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) 。

1. 完整的 **基本概念** 和 **設定** 區段。 選取唯一的叢集和資料庫名稱。
建立 Azure 資料總管叢集和資料庫需要幾分鐘的時間。

1. 若要確認部署，您可以在 [Azure 入口網站](https://portal.azure.com) 中開啟資源群組，以尋找新的叢集和資料庫。 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>使用 powershell 部署範本並確認範本部署

#### <a name="deploy-the-template-using-powershell"></a>使用 powershell 部署範本

1. 從下列程式碼區塊中選取 [ **試試看** ]，然後依照指示登入 Azure Cloud shell。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. 選取 [複製] 來複製 PowerShell 指令碼。
1. 以滑鼠右鍵按一下殼層主控台，然後選取 [貼上]。
建立 Azure 資料總管叢集和資料庫需要幾分鐘的時間。

#### <a name="verify-the-deployment-using-powershell"></a>使用 PowerShell 驗證部署

若要確認部署，請使用下列 Azure PowerShell 腳本。  如果 Cloud Shell 仍開啟，您就不需要複製/執行第一行 (Read-Host)。 如需有關在 PowerShell 中管理 Azure 資料總管資源的詳細資訊，請參閱 [Kusto](/powershell/module/az.kusto/?view=azps-2.7.0)。 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

[將資料內嵌至 Azure 資料總管叢集和資料庫](ingest-data-overview.md)
