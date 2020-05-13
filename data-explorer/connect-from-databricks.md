---
title: 使用 Python 從 Azure Databricks 連線至 Azure 資料總管
description: 本主題說明如何使用 Azure Databricks 中的 Python 程式庫，透過兩種驗證方法之一從 Azure 資料總管存取資料。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 1853b85215da63a753a7867cb9d4354c5b99731a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373972"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>使用 Python 從 Azure Databricks 連線至 Azure 資料總管

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) 是針對 Microsoft Azure 平台進行最佳化的 Apache Spark 分析平台。 本文說明如何使用 Azure Databricks 中的 Python 程式庫從 Azure 資料總管存取資料。 您可以透過數種方式向 Azure 資料總管進行驗證，包括裝置登入和 Azure Active Directory (Azure AD) 應用程式。

## <a name="prerequisites"></a>Prerequisites

- [建立 Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。
- [建立 Azure Databricks 工作區](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)。 在 [Azure Databricks 服務]**** 下方的 [定價層]**** 下拉式清單中，選取 [Premium]****。 此選取項目可讓您使用 Azure Databricks 祕密儲存您的認證，並在 Notebook 和作業中加以參考。

- 在 Azure Databricks 中使用下列規格 (執行範例 Notebook 所需的最低設定) [建立叢集](https://docs.azuredatabricks.net/user-guide/clusters/create.html)：

   ![建立叢集的規格](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>在 Azure Databricks 叢集上安裝 Python 程式庫

若要在 Azure Databricks 叢集上安裝 [Python 程式庫](kusto/api/python/kusto-python-client-library.md)：

1. 移至您的 Azure Databricks 工作區，並[建立程式庫](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)。
2. [上傳 Python PyPI 套件或 Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)。
   - 將程式庫上傳、安裝並連結至 Databricks 叢集。
   - 輸入 PyPi 名稱：**azure-kusto-data**。

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>使用裝置登入連線到 Azure 資料總管

使用 [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) Notebook [匯入 Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook)。 您可以接著使用認證連線到 Azure 資料總管。

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>使用 Azure AD 應用程式連線至 ADX

1. 藉由[佈建 AAD 應用程式](kusto/management/access-control/how-to-provision-aad-app.md)來建立 Azure AD 應用程式。
1. 授與對您 Azure 資料總管資料庫上的 Azure AD 應用程式進行存取的權限，如下所示：

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | 您的資料庫名稱 |
    | ```AAD App ID``` | 您的 Azure AD 應用程式識別碼 |
    | ```AAD Tenant ID``` | 您的 Azure AD 租用戶識別碼 |

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

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>儲存並保護 Azure AD 應用程式識別碼和金鑰 

使用 Azure Databricks [祕密](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)儲存並保護 Azure AD 索引鍵和金鑰，如下所示：
1. [設定 CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)。
1. [安裝 CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 
1. [設定驗證](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication)。
1. 使用下列範例命令設定[祕密](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)：

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>匯入 Notebook
使用 [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) Notebook [匯入 Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook)，以連線至 Azure 資料總管。 以您的叢集名稱、資料庫名稱、Azure AD 租用戶識別碼，更新預留位置值。
