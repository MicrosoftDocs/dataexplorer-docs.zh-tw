---
title: 如何為 Azure 資料資源管理器叢集設定託管識別
description: 瞭解如何為 Azure 資料資源管理器群集配置託管標識。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 900bf815917a4b7c9841860d663a2183b1ab71b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499458"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>為 Azure 資料資源管理器叢集設定託管識別

[Azure 活動目錄中的託管標識](/azure/active-directory/managed-identities-azure-resources/overview)允許群集輕鬆存取其他受 AAD 保護的資源,如 Azure 密鑰保管庫。 標識由 Azure 平臺管理,不需要預配或輪換任何機密。 本文介紹如何為 Azure 數據資源管理器群集創建託管標識。 管理辨識設定目前僅支援[為叢集啟用客戶託管金鑰](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)。

> [!Note]
> 如果 Azure 數據資源管理器群集跨訂閱或租戶遷移,則 Azure 數據資源管理器的託管標識不會按預期方式運行。 應用需要獲取新的標識,這可以通過[禁用](#disable-a-system-assigned-identity)和[重新啟用](#add-a-system-assigned-identity)該功能來完成。 還需要更新下游資源的訪問策略才能使用新標識。

## <a name="add-a-system-assigned-identity"></a>新增系統配置的識別
                                                                                                    
分配與群集關聯的系統分配的標識,如果刪除群集,將被刪除。 群集只能有一個系統分配的標識。 創建具有系統分配的標識的群集需要在群集上設置其他屬性。 系統分配的標識使用 C#、ARM 範本或 Azure 門戶添加,詳情如下。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>使用 Azure 門戶新增系統分配的識別

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

#### <a name="new-azure-data-explorer-cluster"></a>新的 Azure 資料資源管理器叢集

1. [建立 Azure 資料資源管理器叢集](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. 在 **「安全**」選項卡>**系統分配識別**中,選擇 **「打開」。** 要刪除系統分配的標識,請選擇 **"關閉**"。
2. 選擇 **「下一步」標記>** 或**查看 +創建**以建立叢集。

    ![將系統配置的識別加入新叢集](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>現有 Azure 資料資源管理器叢集

1. 打開現有的 Azure 資料資源管理器群集。
1. 在門戶的左側窗格中選擇 **「設置** > **標識**」。。
1. 在 **「識別**窗格>**系統分配**」選項卡中:
   1. 將 **「狀態**」滑動到 **.**
   1. 選取 [儲存]  。
   1. 在彈出視窗中,選擇 **"是**"

    ![新增系統配置的識別](media/managed-identities/turn-system-assigned-identity-on.png)

1. 幾分鐘後,螢幕將顯示: 
  * **物件識別碼**-用於客戶託管金鑰 
  * **角色配置**- 點擊連結配置相關角色

    ![系統分配的身份](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>使用 C 新增系統分配的識別#

#### <a name="prerequisites"></a>Prerequisites

要使用 Azure 資料資源管理員 C# 用戶端設定託管識別,請使用以下方式設定:

* 安裝[Azure 資料資源管理員 (Kusto) NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝[Microsoft.身份模型.用戶端.ActiveDirectory NuGet 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)進行身份驗證。
* [創建可以存取](/azure/active-directory/develop/howto-create-service-principal-portal)資源的 Azure AD 應用程式和服務主體。 在訂閱範圍內添加角色分配,並取得所需的`Directory (tenant) ID`和`Application ID` `Client Secret` 。

#### <a name="create-or-update-your-cluster"></a>建立或更新叢集

1. 使用`Identity`以下屬性建立或更新叢集:

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
    
2. 執行以下指令以檢查群組是否已成功建立或使用識別更新:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果結果`ProvisioningState``Succeeded`包含值,則建立或更新群集,並且應具有以下屬性:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`並`TenantId`替換為 GUID。 屬性`TenantId`標識標識標識所屬的 AAD 租戶。 `PrincipalId`是群集新標識的唯一標識符。 在 AAD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

# <a name="arm-template"></a>[ARM 範本](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理員樣本新增系統分配的識別

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 要瞭解有關部署到 Azure 資料資源管理員的詳細資訊,請參閱[使用 Azure 資源管理員樣本建立 Azure 資料資源管理器叢集和資料庫](create-cluster-database-resource-manager.md)。

添加系統分配的類型告訴 Azure 創建和管理群集的標識。 對於所有 `Microsoft.Kusto/clusters` 型別的資源來說，您可以在資源定義中加入以下屬性，以建立採用身分識別的資源： 

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

建立叢集時,它具有以下其他屬性:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`並`<PRINCIPALID>`替換為 GUID。 屬性`TenantId`標識標識標識所屬的 AAD 租戶。 `PrincipalId`是群集新標識的唯一標識符。 在 AAD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

---

## <a name="disable-a-system-assigned-identity"></a>關閉系統分配的識別

刪除系統分配的標識也會將其從 AAD 中刪除。 刪除群集資源時,系統分配的身份也會從 AAD 自動刪除。 可以通過禁用該功能來刪除系統分配的標識。  使用 C#、ARM 範本或 Azure 門戶刪除系統分配的標識,詳情如下。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>使用 Azure 門戶關閉系統分配的識別

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在門戶的左側窗格中選擇 **「設置** > **標識**」。。
1. 在 **「識別**窗格>**系統分配**」選項卡中:
    1. 將 **「狀態」** 滑塊移動到 **" 關閉**" 。
    1. 選取 [儲存]  。
    1. 在彈出視窗中,選擇 **「是**」以禁用系統分配的標識。 **"識別"** 窗格將恢復為與添加系統分配的標識之前相同的條件。

    ![系統分配的身份關閉](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>使用 C 移除系統分配的識別#

執行以下內容以刪除系統分配的識別:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM 範本](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理員樣本移除系統分配的識別

執行以下內容以刪除系統分配的識別:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料資源管理器群集](security.md)
* 以靜態開啟加密來保護[Azure 資料資源管理員 - Azure 門戶中的叢集](manage-cluster-security.md)。
 * [使用 C 設定客戶託管金鑰#](customer-managed-keys-csharp.md)
 * [使用 Azure 資源管理員樣本設定客戶託管金鑰](customer-managed-keys-resource-manager.md)
