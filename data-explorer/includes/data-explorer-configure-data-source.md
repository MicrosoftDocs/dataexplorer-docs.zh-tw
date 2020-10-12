---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: b6c50e466df1028f11021e75fe572ea1ecb32225
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942651"
---
## <a name="configure-the-data-source"></a>設定資料來源

您可以執行下列步驟，將 Azure 資料總管設定為儀表板工具的資料來源。 我們將在這一節中詳細說明這些步驟：

1. 建立 Azure Active Directory (Azure AD) 服務主體。 您的儀表板工具會使用服務主體來存取 Azure 資料總管服務。

1. 將 Azure AD 服務主體新增至Azure 資料總管資料庫中的「檢視者」** 角色。

1. 根據 Azure AD 服務主體中的資訊，指定您的儀表板工具連接屬性，然後測試連接。

### <a name="create-a-service-principal"></a>建立服務主體

您可以在 [Azure 入口網站](#azure-portal)中或使用 [Azure CLI](#azure-cli) 命令列體驗來建立服務主體。 不論使用哪一種方法，建立服務主體後，您將取得可用於四個連線屬性 (後續步驟會用到) 的值。

#### <a name="azure-portal"></a>Azure 入口網站

1. 若要建立服務主體，請依照 [Azure 入口網站文件](/azure/active-directory/develop/howto-create-service-principal-portal)中的指示。

    1. 在[將應用程式指派給角色](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)的區段中，將**讀者**角色類型指派給您的 Azure 資料總管叢集。

    1. 在 [ [取得登入的值](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) ] 區段中，複製 [步驟： **目錄識別碼** (租使用者識別碼) ]、[ **應用程式識別碼**] 和 [ **密碼**] 中所涵蓋的三個屬性值。

1. 在 Azure 入口網站中，選取 [訂用帳戶]****，然後對您已在其中建立服務主體的訂用帳戶複製其識別碼。

    ![訂用帳戶識別碼 - 入口網站](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. 建立服務主體。 設定適當的範圍和 `reader` 角色類型。

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    如需詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

1. 此命令會傳回結果集，如下所示。 複製三個屬性值：**應用程式識別碼**、**密碼**和**租用戶**。


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. 取得您的訂用帳戶清單。

    ```azurecli
    az account list --output table
    ```

    複製正確的訂用帳戶識別碼。

    ![訂用帳戶識別碼 - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>將服務主體新增至檢視者角色

您現在已有服務主體，您可以將其新增至Azure 資料總管資料庫中的「檢視者」** 角色。 您可以在 Azure 入口網站中的 [權限]**** 底下執行此工作，或在 [查詢]**** 底下使用管理命令來執行。

#### <a name="azure-portal---permissions"></a>Azure 入口網站 - 權限

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。

1. 在 [概觀]**** 區段中，選取具有 StormEvents 範例資料的資料庫。

    ![選取資料庫](media/data-explorer-configure-data-source/select-database.png)

1. 選取 [權限]****，然後 [新增]****。

    ![資料庫權限](media/data-explorer-configure-data-source/database-permissions.png)

1. 在 [新增資料庫權限]**** 底下，選取 [檢視者]**** 角色，然後**選取主體**。

    ![新增資料庫權限](media/data-explorer-configure-data-source/add-permission.png)

1. 搜尋您建立的服務主體。 選取主體，然後**選取**。

    :::image type="content" source="media/data-explorer-configure-data-source/new-principals.png" alt-text="[新增原則] 窗格 Azure 入口網站的螢幕擷取畫面。會反白顯示 [選取] 按鈕和兩個具有無法解讀之服務主體屬性的欄位。" border="false":::

1. 選取 [儲存]****。

    :::image type="content" source="media/data-explorer-configure-data-source/save-permission.png" alt-text="[新增原則] 窗格 Azure 入口網站的螢幕擷取畫面。會反白顯示 [選取] 按鈕和兩個具有無法解讀之服務主體屬性的欄位。" border="false":::

#### <a name="management-command---query"></a>管理命令 - 查詢

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集，然後選取 [查詢]****。

    :::image type="content" source="media/data-explorer-configure-data-source/query.png" alt-text="[新增原則] 窗格 Azure 入口網站的螢幕擷取畫面。會反白顯示 [選取] 按鈕和兩個具有無法解讀之服務主體屬性的欄位。" border="false":::

1. 在查詢視窗中執行下列命令。 從 Azure 入口網站或 CLI 使用應用程式識別碼和租用戶識別碼。

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    此命令會傳回結果集，如下所示。 在此範例中，第一個資料列是資料庫中的現有使用者，第二個資料列是剛才新增的服務主體。

    ![結果集](media/data-explorer-configure-data-source/result-set.png)
