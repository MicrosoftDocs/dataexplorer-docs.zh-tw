---
title: 在 Azure 資料總管中建立叢集期間啟用基礎結構加密（雙重加密）
description: 本文說明如何在 Azure 資料總管中建立叢集期間啟用基礎結構加密（雙重加密）。
author: orspod
ms.author: orspodek
ms.reviewer: toleibov
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 4a550d7596a74c3ae0bfca1718f10a69a183cc58
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515924"
---
# <a name="enable-infrastructure-encryption-double-encryption-during-cluster-creation-in-azure-data-explorer"></a>在 Azure 資料總管中建立叢集期間啟用基礎結構加密（雙重加密）
  
當您建立叢集時，其儲存體會[在服務層級自動加密](/azure/storage/common/storage-service-encryption)。 如果您需要更高層級的保證資料是安全的，您也可以啟用[Azure 儲存體基礎結構層級加密](/azure/storage/common/infrastructure-encryption-enable)（也稱為雙重加密）。 啟用基礎結構加密時，儲存體帳戶中的資料會加密兩次，一次在服務層級，一次在基礎結構層級，使用兩個不同的加密演算法和兩個不同的金鑰。 Azure 儲存體資料的雙重加密可防範其中一個加密演算法或金鑰可能遭到入侵的案例。 在此案例中，額外一層加密會繼續保護您的資料。

> [!IMPORTANT]
> * 只有在叢集建立期間，才可以啟用雙重加密。
> * 在叢集上啟用基礎結構加密之後，您就**無法**將它停用。
> * 雙重加密僅適用于支援基礎結構加密的區域。 如需詳細資訊，請參閱[儲存體基礎結構加密](/azure/storage/common/infrastructure-encryption-enable)。

# <a name="c"></a>[C#](#tab/c-sharp)

您可以使用 c # 在叢集建立期間啟用基礎結構加密。

## <a name="prerequisites"></a>先決條件

使用 Azure 資料總管 c # 用戶端設定受控識別：

* 安裝[Azure 資料總管（Kusto） NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝 Microsoft.identitymodel 的驗證[NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。
* 建立可存取資源的[Azure AD 應用程式](/azure/active-directory/develop/howto-create-service-principal-portal)和服務主體。 您在訂用帳戶範圍加入角色指派，並取得必要的 `Directory (tenant) ID` 、 `Application ID` 和 `Client Secret` 。

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
    
2. 執行下列命令來檢查是否已成功建立您的叢集：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果結果包含 `ProvisioningState` 值為的 `Succeeded` ，則會成功建立叢集。

# <a name="arm-template"></a>[ARM 範本](#tab/arm)

您可以使用 Azure Resource Manager，在叢集建立期間啟用基礎結構加密。

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 若要深入瞭解如何部署至 Azure 資料總管，請參閱[使用 Azure Resource Manager 範本建立 azure 資料總管叢集和資料庫](create-cluster-database-resource-manager.md)。

## <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本新增系統指派的身分識別

1. 新增 ' EnableDoubleEncryption ' 類型，以告知 Azure 為您的叢集啟用基礎結構加密（雙重加密）。

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

2. 建立叢集時，它具有下列其他屬性：

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
