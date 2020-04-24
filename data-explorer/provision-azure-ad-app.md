---
title: 在 Azure 資料資源管理員中建立 Azure AD 應用程式
description: 瞭解如何在 Azure 資料資源管理器中創建 Azure AD 應用程式。
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 7bbb8064cd71e61b6fdfb1205e783099ec21c687
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501590"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>在 Azure 資料資源管理員中建立 Azure 活動目錄應用程式註冊

Azure 活動目錄 (Azure AD) 應用程式身份驗證用於需要存取 Azure 資料資源管理員且使用者不在的情況下的應用程式(如無人參與的服務或計畫流)。 如果使用應用程式(如 Web 應用)連接到 Azure 資料資源管理器資料庫,則應使用服務主體身份驗證進行身份驗證。 本文詳細介紹了如何創建和註冊 Azure AD 服務主體,然後授權它訪問 Azure 數據資源管理器資料庫。

## <a name="create-azure-ad-application-registration"></a>建立 Azure AD 應用程式註冊

Azure AD 應用程式身份驗證需要創建應用程式並將其註冊到 Azure AD。 在 Azure AD 租戶中創建應用程式註冊時,將自動創建服務主體。 

1. 登入[Azure 門戶](https://portal.azure.com)並開啟`Azure Active Directory`邊列選項卡

    ![從門戶選單中選擇 Azure 的動作目錄](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. 選擇**應用註冊**邊欄選項卡並選擇 **"新建註冊"**

    ![開始新的應用程式註冊](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. 填寫下列資訊︰ 

    * **名稱** 
    * **支援的帳戶類型**
    * **重定向 URI** > **Web**
        > [!IMPORTANT] 
        > 應用程式型態應為**Web**。 URI 是可選的,在這種情況下留空。
    * 選擇 **"註冊"**

    ![註冊新應用註冊](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. 選擇 **「概述」** 邊列選項卡並複製**應用程式代碼**。

    > [!NOTE]
    > 您需要應用程式 ID 來授權服務主體訪問資料庫。

    ![複製應用註冊識別碼](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. 在 **'憑證&機密**'邊列選項卡中,選擇 **'新增用戶端機密**"

    ![開始建立用戶端機密](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > 本文介紹對應用程式的憑據使用用戶端機密。  您還可以使用 X509 證書對應用程式進行身份驗證。 選擇 **「上傳證書**」並按照說明上傳證書的公共部分。

1. 輸入說明、過期並選擇 **"添加"**

    ![輸入用戶端機密參數](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. 複製金鑰值。

    > [!NOTE]
    > 離開此頁面時,無法訪問鍵值。  您需要金鑰才能將用戶端憑據配置為資料庫。

    ![複製客戶端金鑰值](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

您的應用程式已創建。 如果只需要訪問授權的 Azure 資料資源管理員資源(如下面的程式設計範例),請跳過下一節。 有關委派許可權支援,請參閱[為應用程式註冊配置委派許可權](#configure-delegated-permissions-for-the-application-registration)。

## <a name="configure-delegated-permissions-for-the-application-registration"></a>為應用程式註冊設定委派權限

如果應用程式需要使用調用使用者的認證 Azure 資料資源管理員,請為應用程式註冊配置委派許可權。 例如,如果要構建 Web API 以造訪 Azure 資料資源管理員,並且希望使用*調用*API 的使用者的認證進行身分驗證。  

1. 在**API 許可權**邊欄選項卡中,選擇 **「添加權限**」。
1. 選擇**我的組織使用的 API。** 搜尋並選擇**Azure 資料資源管理員**。

    ![新增 Azure 資料資源管理員 API 權限](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. 在**委派權限**中,選擇**user_impersonation**框並**新增權限**

    ![通過使用者模擬選擇委派權限](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>授服務主體對 Azure 資料資源管理員資料庫的存取

創建服務主體應用程式註冊后,需要授予對 Azure 數據資源管理器資料庫的相應服務主體訪問許可權。 

1. 在[Web UI](https://dataexplorer.azure.com/)中,連接到資料庫並打開查詢選項卡。

1. 執行下列命令：

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    例如：
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    最後一個參數是查詢與資料庫關聯的角色時以註釋顯示的字串。
    
    > [!NOTE]
    > 創建應用程式註冊后,可能會有幾分鐘的延遲,直到 Azure 資料資源管理器可以引用它。 如果在執行命令時收到找不到應用程式的錯誤,請等待並重試。

有關詳細資訊,請參閱[安全角色管理和](kusto/management/security-roles.md)[引入權限](kusto/api/netfx/kusto-ingest-client-permissions.md)。  

## <a name="using-application-credentials-to-access-a-database"></a>使用應用程式認證資料庫

使用應用程式認證使用[Azure 資料資源管理員用戶端庫](kusto/api/netfx/about-kusto-data.md)以程式設計方式存取資料庫。

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > 指定前面創建的應用程式註冊(服務主體)的應用程式 ID 和密鑰。

有關詳細資訊,請參閱使用[Azure AD 進行 Azure 資料資源管理員存取進行身份驗證](kusto/management/access-control/how-to-authenticate-with-aad.md),並使用[.NET Core Web 應用的 Azure 金鑰保存 。](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)

## <a name="troubleshooting"></a>疑難排解

### <a name="invalid-resource-error"></a>不合法資源錯誤

如果應用程式用於驗證使用者或應用程式以進行 Azure 資料資源管理員訪問,則必須為 Azure 資料資源管理員服務應用程式設置委派許可權。 聲明應用程式可以驗證使用者或應用程式以進行 Azure 資料資源管理員訪問。 如果不這樣做,在進行身份驗證嘗試時,將導致類似於以下內容的錯誤:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

您需要按照有關[為 Azure 資料資源管理員服務應用程式設置委派許可權的說明](#configure-delegated-permissions-for-the-application-registration)進行操作。

### <a name="enable-user-consent-error"></a>啟用使用者同意錯誤

Azure AD 租戶管理員可能會制定策略,防止租戶使用者同意應用程式。 當使用者嘗試登入您的應用程式時,這種情況將導致類似於以下內容的錯誤:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

您需要與 Azure AD 管理員聯繫,以便授予租戶中的所有使用者的同意,或為特定應用程式啟用使用者同意。

## <a name="next-steps"></a>後續步驟

* 有關支援的連線字串的清單,請參閱[Kusto 連接字串](kusto/api/connection-strings/kusto.md)。
