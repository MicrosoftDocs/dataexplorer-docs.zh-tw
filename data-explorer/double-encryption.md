---
title: '在 Azure 資料總管的叢集建立期間，啟用基礎結構加密 (雙重加密) '
description: 本文說明如何在 Azure 資料總管的叢集建立期間，啟用基礎結構加密 (雙重加密) 。
author: orspod
ms.author: orspodek
ms.reviewer: toleibov
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/11/2020
ms.openlocfilehash: 4949190befdc8adcec9f8115305a2a403994395f
ms.sourcegitcommit: 88291fd9cebc26e5210463cb95be5540bf84eef8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437401"
---
# <a name="enable-infrastructure-encryption-double-encryption-during-cluster-creation-in-azure-data-explorer"></a>在 Azure 資料總管的叢集建立期間，啟用基礎結構加密 (雙重加密) 
  
當您建立叢集時，會 [在服務層級自動加密](/azure/storage/common/storage-service-encryption)其儲存體。 如果您需要更高的保證資料安全，也可以啟用 [Azure 儲存體基礎結構層級加密](/azure/storage/common/infrastructure-encryption-enable)，也稱為雙重加密。 啟用基礎結構加密時，儲存體帳戶中的資料會加密兩次，一次是在服務層級進行，一次在基礎結構層級，使用兩個不同的加密演算法和兩個不同的金鑰。 Azure 儲存體資料的雙重加密可防止其中一個加密演算法或金鑰可能遭到入侵的案例。 在此案例中，額外的加密層級會繼續保護您的資料。

> [!IMPORTANT]
> * 只有在建立叢集時，才可以啟用雙重加密。
> * 一旦在您的叢集上啟用基礎結構加密，您就 **無法** 將它停用。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

1. [建立 Azure 資料總管叢集](create-cluster-database-portal.md#create-a-cluster) 
1. 在 [ **安全性** ] 索引標籤中 > **啟用雙重加密**]，然後選取 [ **開啟**]。 若要移除雙重加密，請選取 [ **關閉**]。
1. 選取 **[下一步]： [網路>] 或 [ ** **檢查 + 建立** ] 以建立叢集。

    :::image type="content" source="media/double-encryption/double-encryption-portal.png" alt-text="雙重加密新叢集":::


# <a name="c"></a>[C#](#tab/c-sharp)

您可以使用 c # 在叢集建立期間啟用基礎結構加密。

## <a name="prerequisites"></a>先決條件

使用 Azure 資料總管 c # 用戶端設定受控識別：

* 安裝 [Azure 資料總管 (Kusto) NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝 [Microsoft.identitymodel NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 以進行驗證。
* 建立可存取資源[Azure AD 應用程式](/azure/active-directory/develop/howto-create-service-principal-portal)和服務主體。 您可以在訂用帳戶範圍新增角色指派，並取得必要的 `Directory (tenant) ID` 、 `Application ID` 和 `Client Secret` 。

## <a name="create-your-cluster"></a>建立叢集

1. 使用屬性建立您的叢集 `enableDoubleEncryption` ：

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
                                                                                                    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "East US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var enableDoubleEncryption = true;
    var cluster = new Cluster(location, sku, enableDoubleEncryption: enableDoubleEncryption);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
1. 執行下列命令來檢查是否已成功建立您的叢集：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果結果包含 `ProvisioningState` `Succeeded` 值，就表示已成功建立叢集。

# <a name="arm-template"></a>[ARM 範本](#tab/arm)

您可以使用 Azure Resource Manager 在叢集建立期間啟用基礎結構加密。

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 若要深入瞭解如何部署至 Azure 資料總管，請參閱 [使用 Azure Resource Manager 範本建立 azure 資料總管叢集和資料庫](create-cluster-database-resource-manager.md)。

## <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本新增系統指派的身分識別

1. 新增 ' EnableDoubleEncryption ' 類型，以告訴 Azure 為您的叢集啟用基礎結構加密 (雙重加密) 。
    
    ```json
    {
        "apiVersion": "2020-06-14",
        "type": "Microsoft.Kusto/clusters",
        "name": "[variables('clusterName')]",
        "location": "[resourceGroup().location]",
        "properties": {
            "trustedExternalTenants": [],
            "virtualNetworkConfiguration": null,
            "optimizedAutoscale": null,
            "enableDiskEncryption": false,
            "enableStreamingIngest": false,
            "enableDoubleEncryption": true,
        }
    }
    ```

1. 建立叢集時，它會有下列額外屬性：

    ```json
    "identity": {
        "type": "SystemAssigned",
        "tenantId": "<TENANTID>",
        "principalId": "<PRINCIPALID>"
    }
    ```
---

## <a name="next-steps"></a>後續步驟

[檢查叢集健康情況](check-cluster-health.md)
