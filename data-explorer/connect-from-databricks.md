---
title: 從 Azure Databricks 連接到 Azure 資料總管
description: 本主題說明如何使用 Azure Databricks 來存取 Azure 資料總管的資料。
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 08ac06b5f0a1a65afec6a71106943f3b58c1b9f5
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610299"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks"></a>從 Azure Databricks 連接到 Azure 資料總管

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) 是針對 Microsoft Azure 平台進行最佳化的 Apache Spark 分析平台。 本文說明如何使用 Azure Databricks 來存取 Azure 資料總管的資料。 您可以透過數種方式向 Azure 資料總管進行驗證，包括裝置登入和 Azure Active Directory (Azure AD) 應用程式。
 
## <a name="prerequisites"></a>Prerequisites

- [建立 Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。
- [建立 Azure Databricks 工作區](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)。 在 [Azure Databricks 服務]**** 下方的 [定價層]**** 下拉式清單中，選取 [Premium]****。 此選取項目可讓您使用 Azure Databricks 祕密儲存您的認證，並在 Notebook 和作業中加以參考。

- 使用預設設定在 Azure Databricks 中[建立](https://docs.azuredatabricks.net/user-guide/clusters/create.html)叢集。

 ## <a name="install-the-kusto-spark-connector-on-your-azure-databricks-cluster"></a>在 Azure Databricks 叢集上安裝 Kusto Spark 連接器

若要在 Azure Databricks 叢集上安裝 [spark-kusto 連接器](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector) ：

1. 移至您的 Azure Databricks 工作區，並[建立程式庫](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)。
1. 在 Maven Central 上搜尋 *spark kusto 連接器* 套件、安裝最新版本，然後附加至您的叢集。 

## <a name="connect-to-azure-data-explorer-by-using-a-device-authentication"></a>使用裝置驗證連接到 Azure 資料總管

[範例程式碼](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py)。

## <a name="connect-to-azure-data-explorer-by-using-an-azure-ad-app"></a>使用 Azure AD 應用程式連接至 Azure 資料總管

1. 藉由[佈建 AAD 應用程式](kusto/management/access-control/how-to-provision-aad-app.md)來建立 Azure AD 應用程式。
1. 授與對您 Azure 資料總管資料庫上的 Azure AD 應用程式進行存取的權限，如下所示：

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```

    | 參數 | 描述 |
    | - | - |
    | `DB Name` | 您的資料庫名稱 |
    | `AAD App ID` | 您的 Azure AD 應用程式識別碼 |
    | `AAD Tenant ID` | 您的 Azure AD 租用戶識別碼 |

### <a name="find-your-azure-ad-tenant-id"></a>尋找您的 Azure AD 租用戶識別碼

為了驗證應用程式，Azure 資料總管會使用您的 Azure AD 租用戶識別碼。 若要尋找您的租用戶識別碼，請使用下列 URL。 以您的網域替代 *YourDomain*。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

例如，如果您的網域為 *contoso.com*，則 URL 會是：[https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)。 選取此 URL 來查看結果。 第一行如下所示： 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

您的租用戶識別碼為 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`。 

### <a name="store-and-secure-your-azure-ad-app-id-and-key-optional"></a>儲存並保護您 Azure AD 的應用程式識別碼和金鑰 (選擇性)   

使用 Azure Databricks [祕密](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)儲存並保護 Azure AD 索引鍵和金鑰，如下所示：

1. [設定 CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)。
1. [安裝 CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 
1. [設定驗證](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication)。
1. 使用下列範例命令設定[祕密](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)：

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="sample-code"></a>範例程式碼

1. [範例程式碼](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py)。 
1. 使用您的叢集名稱、資料庫名稱、資料表名稱、Azure AD 租使用者識別碼、AAD 應用程式識別碼和 AAD 應用程式金鑰，更新預留位置值。 如果您要將認證儲存在 databricks 秘密存放區中，請視需要更新程式碼以從 dbutils 取得值。
