---
title: 使用 C 設定客戶管理的金鑰#
description: 本文說明如何在 Azure 資料總管中，針對您的資料設定客戶管理的金鑰加密。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: ff2edd1e64aa3ef44c96ecf15d6a859eadd49e69
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257870"
---
# <a name="configure-customer-managed-keys-using-c"></a>使用 C 設定客戶管理的金鑰#

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

本節說明如何使用 Azure 資料總管 c # 用戶端來設定客戶管理的金鑰加密。 

### <a name="prerequisites"></a>Prerequisites

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

### <a name="install-c-nuget"></a>安裝 c # NuGet

* 安裝[Azure 資料總管（Kusto） NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。

* 安裝 Microsoft.identitymodel 的驗證[NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。

### <a name="authentication"></a>驗證

若要執行本文中的範例，請建立可存取資源的[Azure AD 應用程式](/azure/active-directory/develop/howto-create-service-principal-portal)和服務主體。 您可以在訂用帳戶範圍加入角色指派，並取得所需的 `Directory (tenant) ID` 、 `Application ID` 和 `Client Secret` 。

### <a name="configure-cluster"></a>設定叢集

根據預設，Azure 資料總管加密會使用 Microsoft 管理的金鑰。 將您的 Azure 資料總管叢集設定為使用客戶管理的金鑰，並指定要與叢集產生關聯的金鑰。

1. 使用下列程式碼更新您的叢集：

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

1. 執行下列命令，以檢查您的叢集是否已成功更新：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果結果包含 `ProvisioningState` 值為的 `Succeeded` ，則已成功更新您的叢集。

## <a name="update-the-key-version"></a>更新金鑰版本

當您建立新版本的金鑰時，您必須更新叢集以使用新的版本。 首先，呼叫 `Get-AzKeyVaultKey` 以取得金鑰的最新版本。 然後，將叢集的 key vault 屬性更新為使用新版本的金鑰，如[設定](#configure-cluster)叢集中所示。

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料總管叢集](security.md)
* [設定 Azure 資料總管叢集的受控識別](managed-identities.md)
* 藉由啟用待用加密，[在 Azure 資料總管 Azure 入口網站中保護您](manage-cluster-security.md)的叢集。
* [使用 Azure Resource Manager 範本設定客戶管理的金鑰](customer-managed-keys-resource-manager.md)


