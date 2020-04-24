---
title: 使用 C 設定客戶託管金鑰#
description: 本文介紹如何在 Azure 資料資源管理員中配置客戶管理的數據金鑰加密。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e394d928774624ac3c7faacab7726570272da82a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497482"
---
# <a name="configure-customer-managed-keys-using-c"></a>使用 C 設定客戶託管金鑰#

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

本節介紹如何使用 Azure 數據資源管理員 C# 用戶端配置客戶管理金鑰加密。 

### <a name="prerequisites"></a>Prerequisites

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 請確保在視覺化工作室設定期間開啟**Azure 開發**。

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

### <a name="install-c-nuget"></a>安裝 C# NuGet

* 安裝[Azure 資料資源管理員 (Kusto) NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。

* 安裝[Microsoft.身份模型.用戶端.ActiveDirectory NuGet 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)進行身份驗證。

### <a name="authentication"></a>驗證

要執行本文中的範例,[請建立一個](/azure/active-directory/develop/howto-create-service-principal-portal)可以存取資源的 Azure AD 應用程式和服務主體。 您可以在訂閱範圍內添加角色分配,並獲得所需的`Directory (tenant) ID``Application ID`和`Client Secret`。

### <a name="configure-cluster"></a>設定叢集

默認情況下,Azure 資料資源管理員加密使用Microsoft管理的密鑰。 將 Azure 資料資源管理器群集配置為使用客戶管理的密鑰,並指定要與群集關聯的密鑰。

1. 使用以下代碼更新叢集:

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
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. 執行以下指令以檢查群組是否已成功更新:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果結果`ProvisioningState``Succeeded`包含 該值,則群集已成功更新。

## <a name="update-the-key-version"></a>更新金鑰版本

建立金鑰的新版本時,需要更新群集才能使用新版本。 首先,打電話`Get-AzKeyVaultKey`獲取最新版本的密鑰。 然後更新群集的密鑰保管庫屬性以使用密鑰的新版本,如[配置群集](#configure-cluster)所示。

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料資源管理器群集](security.md)
* [為 Azure 資料資源管理器叢集設定託管識別](managed-identities.md)
* 以靜態開啟加密來保護[Azure 資料資源管理員 - Azure 門戶中的叢集](manage-cluster-security.md)。
* [使用 Azure 資源管理員樣本設定客戶託管金鑰](customer-managed-keys-resource-manager.md)


