---
title: 如何設定 Azure 資料總管叢集的受控識別
description: 瞭解如何設定 Azure 資料總管叢集的受控識別。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 2bfc2cd69d88395e04af16e732564fb90170ee7f
ms.sourcegitcommit: 7edce9d9d20f9c0505abda67bb8cc3d2ecd60d15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524278"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>設定 Azure 資料總管叢集的受控識別

[Azure Active Directory 的受控識別](/azure/active-directory/managed-identities-azure-resources/overview)可讓您的叢集輕鬆地存取其他受 Azure AD 保護的資源，例如 Azure Key Vault。 身分識別是由 Azure 平臺所管理，而且您不需要布建或輪替任何秘密。 受控識別設定目前僅支援為 [您的叢集啟用客戶管理的金鑰](security.md#customer-managed-keys-with-azure-key-vault)。

您的 Azure 資料總管叢集可獲得兩種類型的身分識別：

* **系統指派的身分識別**：系結至您的叢集，並在刪除您的資源時予以刪除。 叢集只能有一個系統指派的身分識別。
* **使用者指派的身分識別**：可指派給叢集的獨立 Azure 資源。 叢集可以有多個使用者指派的身分識別。

本文說明如何為 Azure 資料總管叢集新增和移除系統指派的受控識別和使用者指派的受控識別。

> [!Note]
> 如果您的 Azure 資料總管叢集在訂用帳戶或租使用者之間遷移，Azure 資料總管的受控識別將無法如預期般運作。 應用程式將需要取得新的身分識別，您可以藉由 [停](#remove-a-system-assigned-identity) 用並 [重新啟用](#add-a-system-assigned-identity) 功能來完成。 您也必須更新下游資源的存取原則，才能使用新的身分識別。

## <a name="add-a-system-assigned-identity"></a>新增系統指派的身分識別

指派系結至您叢集的系統指派身分識別，並在刪除您的叢集時將其刪除。 叢集只能有一個系統指派的身分識別。 使用系統指派的身分識別建立叢集時，必須在叢集上設定額外的屬性。 使用 Azure 入口網站、c # 或 Resource Manager 範本新增系統指派的身分識別，如下所述。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>使用 Azure 入口網站新增系統指派的身分識別

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

#### <a name="new-azure-data-explorer-cluster"></a>新的 Azure 資料總管叢集

1. [建立 Azure 資料總管叢集](create-cluster-database-portal.md#create-a-cluster) 
1. 在 [ **安全性** ] 索引標籤中 > **系統指派** 的身分識別，然後選取 [ **開啟**] 若要移除系統指派的身分識別，請選取 [ **關閉**]。
1. 選取 **[下一步：標記] >或 [** **檢查 + 建立** ] 以建立叢集。

    ![將系統指派的身分識別新增至新的叢集](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>現有的 Azure 資料總管叢集

1. 開啟現有的 Azure 資料總管叢集。
1. **Settings**  >  在入口網站的左窗格中選取 [設定身分 **識別**]。
1. 在 [身分 **識別** ] 窗格中 > **系統指派** ] 索引標籤：
   1. 將 [ **狀態** ] 滑杆移至 [ **開啟**]。
   1. 選取 [儲存]
   1. 在快顯視窗中，選取 **[是**]

    ![新增系統指派的身分識別](media/managed-identities/turn-system-assigned-identity-on.png)

1. 幾分鐘後，畫面會顯示：
    * **物件識別碼** -用於客戶管理的金鑰
    * **許可權** -選取相關的角色指派

    ![系統指派的身分識別](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>使用 C 新增系統指派的身分識別#

#### <a name="prerequisites"></a>先決條件

若要使用 Azure 資料總管 c # 用戶端設定受控識別：

* 安裝 [Azure 資料總管 (Kusto) NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝 [Microsoft.identitymodel NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 以進行驗證。
* 建立可存取資源[Azure AD 應用程式](/azure/active-directory/develop/howto-create-service-principal-portal)和服務主體。 您可以在訂用帳戶範圍新增角色指派，並取得必要的 `Directory (tenant) ID` 、 `Application ID` 和 `Client Secret` 。

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

2. 執行下列命令，以檢查您的叢集是否已順利建立或更新為身分識別：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果結果包含 `ProvisioningState` `Succeeded` 值，則會建立或更新叢集，且應該具有下列屬性：

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId` 並 `TenantId` 以 guid 取代。 `TenantId`屬性會識別身分識別所屬的 Azure AD 租使用者。 `PrincipalId`是叢集新身分識別的唯一識別碼。 在 Azure AD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

# <a name="resource-manager-template"></a>[Resource Manager 範本](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本新增系統指派的身分識別

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 若要深入瞭解如何部署至 Azure 資料總管，請參閱 [使用 Azure Resource Manager 範本建立 azure 資料總管叢集和資料庫](create-cluster-database-resource-manager.md)。

新增系統指派的類型可告知 Azure 建立及管理叢集的身分識別。 對於所有 `Microsoft.Kusto/clusters` 型別的資源來說，您可以在資源定義中加入以下屬性，以建立採用身分識別的資源：

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
    }
}
```

> [!NOTE]
> 叢集可以同時具有系統指派和使用者指派的身分識別。 `type`屬性會是`SystemAssigned,UserAssigned`

建立叢集時，它會有下列額外屬性：

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` 並 `<PRINCIPALID>` 以 guid 取代。 `TenantId`屬性會識別身分識別所屬的 Azure AD 租使用者。 `PrincipalId`是叢集新身分識別的唯一識別碼。 在 Azure AD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

---

## <a name="remove-a-system-assigned-identity"></a>移除系統指派的身分識別

移除系統指派的身分識別也會將它從 Azure AD 中刪除。 當刪除叢集資源時，系統也會自動從 Azure AD 中移除系統指派的身分識別。 藉由停用此功能，可以移除系統指派的身分識別。 使用 Azure 入口網站、c # 或 Resource Manager 範本移除系統指派的身分識別，如下所述。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

### <a name="remove-a-system-assigned-identity-using-the-azure-portal"></a>使用 Azure 入口網站移除系統指派的身分識別

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. **Settings**  >  在入口網站的左窗格中選取 [設定身分 **識別**]。
1. 在 [身分 **識別** ] 窗格中 > **系統指派** ] 索引標籤：
    1. 將 [ **狀態** ] 滑杆移至 [ **關閉**]。
    1. 選取 [儲存]
    1. 在快顯視窗中，選取 [ **是** ] 以停用系統指派的身分識別。 在新增系統指派的身分識別之前，[身分 **識別** ] 窗格會還原為相同的條件。

    ![系統指派的身分識別關閉](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>使用 C 移除系統指派的身分識別#

執行下列動作，以移除系統指派的身分識別：

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="resource-manager-template"></a>[Resource Manager 範本](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本移除系統指派的身分識別

執行下列動作，以移除系統指派的身分識別：

```json
"identity": {
    "type": "None"
}
```

> [!NOTE]
> 如果叢集同時具有系統指派的身分識別和使用者指派的身分識別，則在系統指派的身分識別移除之後， `type` 屬性會是 `UserAssigned`

---

## <a name="add-a-user-assigned-identity"></a>新增使用者指派的身分識別

將使用者指派的受控識別指派給您的叢集。 叢集可以有一個以上的使用者指派身分識別。 使用使用者指派的身分識別來建立叢集時，必須在叢集上設定額外的屬性。 使用 Azure 入口網站、c # 或 Resource Manager 範本新增使用者指派的身分識別，如下所述。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

### <a name="add-a-user-assigned-identity-using-the-azure-portal"></a>使用 Azure 入口網站新增使用者指派的身分識別

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. [建立使用者指派的受控識別資源](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal#create-a-user-assigned-managed-identity)。
1. 開啟現有的 Azure 資料總管叢集。
1. **Settings**  >  在入口網站的左窗格中選取 [設定身分 **識別**]。
1. 在 [ **使用者指派** ] 索引標籤中，選取 [ **新增**]。
1. 搜尋您之前建立的身分識別，並加以選取。 選取 [新增]  。

    ![新增使用者指派的身分識別](media/managed-identities/user-assigned-identity-select.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-user-assigned-identity-using-c"></a>使用 C 新增使用者指派的身分識別#

#### <a name="prerequisites"></a>先決條件

若要使用 Azure 資料總管 c # 用戶端設定受控識別：

* 安裝 [Azure 資料總管 (Kusto) NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝 [Microsoft.identitymodel NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 以進行驗證。
* 建立可存取資源[Azure AD 應用程式](/azure/active-directory/develop/howto-create-service-principal-portal)和服務主體。 您可以在訂用帳戶範圍新增角色指派，並取得必要的 `Directory (tenant) ID` 、 `Application ID` 和 `Client Secret` 。

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
    var identityName = "myIdentity";
    var userIdentityResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}";
    var userAssignedIdentities = new Dictionary<string, IdentityUserAssignedIdentitiesValue>(1) { { userIdentityResourceId, new IdentityUserAssignedIdentitiesValue() } };
    var identity = new Identity(type: IdentityType.UserAssigned, userAssignedIdentities: userAssignedIdentities);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

2. 執行下列命令，以檢查您的叢集是否已順利建立或更新為身分識別：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果結果包含 `ProvisioningState` `Succeeded` 值，則會建立或更新叢集，且應該具有下列屬性：

    ```csharp
    var userIdentity = cluster.Identity.UserAssignedIdentities[userIdentityResourceId];
    var principalId = userIdentity.PrincipalId;
    var clientId = userIdentity.ClientId;
    ```

`PrincipalId`是用於 Azure AD 管理之身分識別的唯一識別碼。 `ClientId`是應用程式新身分識別的唯一識別碼，用來指定執行時間呼叫期間要使用的身分識別。

# <a name="resource-manager-template"></a>[Resource Manager 範本](#tab/arm)

### <a name="add-a-user-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本新增使用者指派的身分識別

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 若要深入瞭解如何部署至 Azure 資料總管，請參閱 [使用 Azure Resource Manager 範本建立 azure 資料總管叢集和資料庫](create-cluster-database-resource-manager.md)。

您可以使用使用者指派的身分識別來建立任何類型的資源 `Microsoft.Kusto/clusters` ，方法是在資源定義中包含下列屬性，並以所 `<RESOURCEID>` 需身分識別的資源識別碼取代：

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
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
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

建立叢集時，它會有下列額外屬性：

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`PrincipalId`是用於 Azure AD 管理之身分識別的唯一識別碼。 `ClientId`是應用程式新身分識別的唯一識別碼，用來指定執行時間呼叫期間要使用的身分識別。

> [!NOTE]
> 叢集可以同時具有系統指派和使用者指派的身分識別。 在此情況下， `type` 屬性會是 `SystemAssigned,UserAssigned` 。

---

## <a name="remove-a-user-assigned-managed-identity-from-a-cluster"></a>從叢集中移除使用者指派的受控識別

使用 Azure 入口網站、c # 或 Resource Manager 範本移除使用者指派的身分識別，如下所述。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

### <a name="remove-a-user-assigned-managed-identity-using-the-azure-portal"></a>使用 Azure 入口網站移除使用者指派的受控識別

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. **Settings**  >  在入口網站的左窗格中選取 [設定身分 **識別**]。
1. 選取 [ **使用者指派** ] 索引標籤。
1. 搜尋您之前建立的身分識別，並加以選取。 選取 [移除]。

    ![移除使用者指派的身分識別](media/managed-identities/user-assigned-identity-remove.png)

1. 在快顯視窗中，選取 [ **是** ] 以移除使用者指派的身分識別。 在加入使用者指派的身分識別之前，[ **識別** ] 窗格會還原為相同的條件。

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-user-assigned-identity-using-c"></a>使用 C 移除使用者指派的身分識別#

執行下列動作，以移除使用者指派的身分識別：

```csharp
var identityName = "myIdentity";
var userIdentityResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}";
var userAssignedIdentities = new Dictionary<string, IdentityUserAssignedIdentitiesValue>(1) { { userIdentityResourceId, null } };
var identity = new Identity(type: IdentityType.UserAssigned, userAssignedIdentities: userAssignedIdentities);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="resource-manager-template"></a>[Resource Manager 範本](#tab/arm)

### <a name="remove-a-user-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本移除使用者指派的身分識別

執行下列動作，以移除使用者指派的身分識別：

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": null
    }
}
```

> [!NOTE]
>
> * 若要移除身分識別，請將其值設定為 null。 所有其他現有的身分識別都不會受到影響。
> * 若要移除所有使用者指派的身分識別 `type` ，屬性會是 `None` ，
> * 如果叢集同時具有系統指派和使用者指派的身分識別，則 `type` 屬性會 `SystemAssigned,UserAssigned` 包含要移除的身分識別，或 `SystemAssigned` 移除所有使用者指派的身分識別。

---

## <a name="next-steps"></a>後續步驟

* [保護 Azure 中的 Azure 資料總管叢集](security.md)
* [在 Azure 中使用磁片加密來保護您的叢集資料總管 Azure 入口網站](cluster-disk-encryption.md) ，方法是啟用待用加密。
* [使用 C 設定客戶管理的金鑰#](customer-managed-keys-csharp.md)
* [使用 Azure Resource Manager 範本設定客戶管理的金鑰](customer-managed-keys-resource-manager.md)
