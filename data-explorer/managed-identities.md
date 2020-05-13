---
title: 如何設定 Azure 資料總管叢集的受控識別
description: 瞭解如何設定 Azure 資料總管叢集的受控識別。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 523330f5ace4d9f2d652eccbd746b039d66df749
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374289"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>設定 Azure 資料總管叢集的受控識別

[Azure Active Directory 的受控識別](/azure/active-directory/managed-identities-azure-resources/overview)可讓您的叢集輕鬆地存取其他受 AAD 保護的資源，例如 Azure Key Vault。 身分識別是由 Azure 平臺所管理，而且不需要布建或輪替任何秘密。 本文說明如何為 Azure 資料總管叢集建立受控識別。 受控識別設定目前僅支援[針對您的叢集啟用客戶管理的金鑰](security.md#customer-managed-keys-with-azure-key-vault)。

> [!Note]
> 如果您的 Azure 資料總管叢集在訂用帳戶或租使用者之間遷移，Azure 資料總管的受控識別不會如預期般運作。 應用程式將需要取得新的身分識別，您可以[停](#disable-a-system-assigned-identity)用再[重新啟用](#add-a-system-assigned-identity)功能來完成此作業。 下游資源的存取原則也需要更新，才能使用新的身分識別。

## <a name="add-a-system-assigned-identity"></a>新增系統指派的身分識別
                                                                                                    
指派系結至您叢集的系統指派身分識別，並在刪除叢集時刪除。 一個叢集只能有一個系統指派的身分識別。 使用系統指派的身分識別建立叢集時，需要在叢集上設定額外的屬性。 系統指派的身分識別會使用 c #、ARM 範本或 Azure 入口網站來新增，如下所述。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>使用 Azure 入口網站新增系統指派的身分識別

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

#### <a name="new-azure-data-explorer-cluster"></a>新的 Azure 資料總管叢集

1. [建立 Azure 資料總管叢集](create-cluster-database-portal.md#create-a-cluster) 
1. 在 [**安全性**] 索引標籤中 >**系統指派**的身分識別，選取 [**開啟**]。 若要移除系統指派的身分識別，請選取 [**關閉**]。
2. 選取 **[下一步]： [標籤]>或 [** **檢查 + 建立**] 來建立叢集。

    ![將系統指派的身分識別新增至新的叢集](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>現有的 Azure 資料總管叢集

1. 開啟現有的 Azure 資料總管叢集。
1. **Settings**  >  在入口網站的左窗格中選取 [設定] [身分**識別**]。
1. 在 [身分**識別**] 窗格中 >**系統指派**] 索引標籤：
   1. 將 [**狀態**] 滑杆移至 [**開啟**]。
   1. 選取 [儲存]  。
   1. 在快顯視窗中，選取 **[是]**

    ![新增系統指派的身分識別](media/managed-identities/turn-system-assigned-identity-on.png)

1. 幾分鐘後，畫面就會顯示： 
  * **物件識別碼**-用於客戶管理的金鑰 
  * **角色指派**-按一下 [連結] 以指派相關角色

    ![系統指派的身分識別于](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>使用 C 新增系統指派的身分識別#

#### <a name="prerequisites"></a>Prerequisites

使用 Azure 資料總管 c # 用戶端設定受控識別：

* 安裝[Azure 資料總管（Kusto） NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝 Microsoft.identitymodel 的驗證[NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。
* 建立可存取資源的[Azure AD 應用程式](/azure/active-directory/develop/howto-create-service-principal-portal)和服務主體。 您在訂用帳戶範圍加入角色指派，並取得必要的 `Directory (tenant) ID` 、 `Application ID` 和 `Client Secret` 。

#### <a name="create-or-update-your-cluster"></a>建立或更新您的叢集

1. 使用屬性建立或更新您的叢集 `Identity` ：

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
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. 執行下列命令，以檢查您的叢集是否已成功建立或更新為身分識別：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果結果包含 `ProvisioningState` 值為的 `Succeeded` ，則會建立或更新叢集，而且應該具有下列屬性：

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`和 `TenantId` 會以 guid 取代。 `TenantId`屬性會識別身分識別所屬的 AAD 租使用者。 `PrincipalId`是叢集新身分識別的唯一識別碼。 在 AAD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

# <a name="arm-template"></a>[ARM 範本](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本新增系統指派的身分識別

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 若要深入瞭解如何部署至 Azure 資料總管，請參閱[使用 Azure Resource Manager 範本建立 azure 資料總管叢集和資料庫](create-cluster-database-resource-manager.md)。

新增系統指派的類型，會告訴 Azure 為您的叢集建立及管理身分識別。 對於所有 `Microsoft.Kusto/clusters` 型別的資源來說，您可以在資源定義中加入以下屬性，以建立採用身分識別的資源： 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

例如：

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

建立叢集時，它具有下列其他屬性：

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`和 `<PRINCIPALID>` 會以 guid 取代。 `TenantId`屬性會識別身分識別所屬的 AAD 租使用者。 `PrincipalId`是叢集新身分識別的唯一識別碼。 在 AAD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

---

## <a name="disable-a-system-assigned-identity"></a>停用系統指派的身分識別

移除系統指派的身分識別，也會將它從 AAD 中刪除。 刪除叢集資源時，系統指派的身分識別也會自動從 AAD 移除。 您可以藉由停用此功能來移除系統指派的身分識別。  系統指派的身分識別會使用 c #、ARM 範本或 Azure 入口網站移除，如下所述。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>使用 Azure 入口網站停用系統指派的身分識別

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. **Settings**  >  在入口網站的左窗格中選取 [設定] [身分**識別**]。
1. 在 [身分**識別**] 窗格中 >**系統指派**] 索引標籤：
    1. 將 [**狀態**] 滑杆移至 [**關閉**]。
    1. 選取 [儲存]  。
    1. 在快顯視窗中，選取 **[是]** 以停用系統指派的身分識別。 [身分**識別**] 窗格會還原成與新增系統指派的身分識別相同的條件。

    ![系統指派的身分識別關閉](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>使用 C 移除系統指派的身分識別#

執行下列動作以移除系統指派的身分識別：

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM 範本](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本移除系統指派的身分識別

執行下列動作以移除系統指派的身分識別：

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料總管叢集](security.md)
* 藉由啟用待用加密，[在 Azure 資料總管 Azure 入口網站中保護您](manage-cluster-security.md)的叢集。
 * [使用 C 設定客戶管理的金鑰#](customer-managed-keys-csharp.md)
 * [使用 Azure Resource Manager 範本設定客戶管理的金鑰](customer-managed-keys-resource-manager.md)
