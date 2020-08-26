---
title: 在 Azure 中建立 Azure AD 應用程式資料總管
description: 瞭解如何在 Azure 資料總管中建立 Azure AD 應用程式。
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/01/2020
ms.openlocfilehash: 5cffee705c6a9225112e7ada8154084de40035c4
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875016"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>在 Azure 中建立 Azure Active Directory 應用程式註冊資料總管

Azure Active Directory (Azure AD) 應用程式驗證適用于需要存取 Azure 資料總管而不需要使用者的應用程式，例如自動服務或排程的流程。 如果您要使用應用程式（例如 web 應用程式）連接到 Azure 資料總管資料庫，您應該使用服務主體驗證進行驗證。 本文將詳細說明如何建立和註冊 Azure AD 服務主體，然後授權它存取 Azure 資料總管資料庫。

## <a name="create-azure-ad-application-registration"></a>建立 Azure AD 應用程式註冊

Azure AD 應用程式驗證需要使用 Azure AD 來建立和註冊應用程式。 在 Azure AD 租使用者中建立應用程式註冊時，會自動建立服務主體。 

1. 登入[Azure 入口網站](https://portal.azure.com)並開啟分頁 `Azure Active Directory`

    ![從入口網站功能表中選取 Azure Active Directory](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. 選取**應用程式註冊**的分頁，然後選取 [**新增註冊**]

    ![開始新的應用程式註冊](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. 填寫下列資訊︰ 

    * **名稱** 
    * **支援的帳戶類型**
    * 重新**導向 URI**  > **Web**
        > [!IMPORTANT] 
        > 應用程式類型應該是 **Web**。 URI 是選擇性的，在此情況下會保留空白。
    * 選取 [註冊]

    ![註冊新的應用程式註冊](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. 選取 **總覽** 分頁，然後複製 **應用程式識別碼**。

    > [!NOTE]
    > 您將需要應用程式識別碼，以授權服務主體存取資料庫。

    ![複製應用程式註冊識別碼](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. 在 [**憑證 & 秘密**] 分頁中，選取 [**新增用戶端密碼**]

    ![開始建立用戶端密碼](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > 本文說明如何使用用戶端秘密作為應用程式的認證。  您也可以使用 X509 憑證來驗證您的應用程式。 選取 [ **上傳憑證** ]，然後依照指示上傳憑證的公開部分。

1. 輸入描述、到期日，然後選取 [**新增**]

    ![輸入用戶端秘密參數](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. 複製金鑰值。

    > [!NOTE]
    > 當您離開此頁面時，將無法存取此金鑰值。  您將需要用來設定資料庫用戶端認證的金鑰。

    ![複製用戶端秘密金鑰值](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

系統會建立您的應用程式。 如果您只需要存取授權的 Azure 資料總管資源（例如，在下面的程式設計範例中），請略過下一節。 如需委派的許可權支援，請參閱 [設定應用程式註冊的委派許可權](#configure-delegated-permissions-for-the-application-registration)。

## <a name="configure-delegated-permissions-for-the-application-registration"></a>設定委派的應用程式註冊許可權

如果您的應用程式需要使用呼叫使用者的認證來存取 Azure 資料總管，請為您的應用程式註冊設定委派的許可權。 例如，如果您要建立 web API 來存取 Azure 資料總管而且想要使用 *呼叫* API 之使用者的認證進行驗證。  

1. 在 [ **API 許可權** ] 分頁中，選取 [ **新增許可權**]。
1. 選取 **我的組織使用的 api**。 搜尋並選取 [ **Azure 資料總管**]。

    ![新增 Azure 資料總管 API 許可權](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. 在 [ **委派的許可權**] 中，選取 **User_impersonation** box 並 **新增許可權**

    ![選取具有使用者模擬的委派許可權](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>將服務主體存取權授與 Azure 資料總管資料庫

現在您已建立服務主體應用程式註冊，您必須將對應的服務主體存取權授與您的 Azure 資料總管資料庫。 

1. 在 [WEB UI](https://dataexplorer.azure.com/)中，連接至您的資料庫，然後開啟 [查詢] 索引標籤。

1. 執行以下命令：

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    例如：
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    最後一個參數是當您查詢與資料庫相關聯的角色時，顯示為附注的字串。
    
    > [!NOTE]
    > 建立應用程式註冊之後，可能會有幾分鐘的延遲，直到 Azure 資料總管可以參考它。 如果在執行命令時收到錯誤訊息，指出找不到應用程式，請稍候，然後再試一次。

如需詳細資訊，請參閱 [安全性角色管理](kusto/management/security-roles.md) 和內嵌 [許可權](kusto/api/netfx/kusto-ingest-client-permissions.md)。  

## <a name="using-application-credentials-to-access-a-database"></a>使用應用程式認證來存取資料庫

使用應用程式認證，以程式設計方式使用 [Azure 資料總管用戶端程式庫](kusto/api/netfx/about-kusto-data.md)來存取您的資料庫。

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
   > 指定應用程式註冊 (服務主體) 稍早建立的應用程式識別碼和金鑰。

如需詳細資訊，請參閱使用 [Azure 的 Azure AD 進行驗證資料總管存取](kusto/management/access-control/how-to-authenticate-with-aad.md) ，並 [使用 Azure Key Vault 搭配 .net Core web 應用程式](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)。

## <a name="troubleshooting"></a>疑難排解

### <a name="invalid-resource-error"></a>不正確資源錯誤

如果您的應用程式是用來驗證 Azure 資料總管存取的使用者或應用程式，您必須設定 Azure 資料總管服務應用程式的委派許可權。 宣告您的應用程式可以驗證 Azure 資料總管存取的使用者或應用程式。 若未這麼做，則會在嘗試進行驗證時產生類似下列的錯誤：

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

您必須遵循 [設定 Azure 資料總管 service 應用程式委派許可權](#configure-delegated-permissions-for-the-application-registration)的指示。

### <a name="enable-user-consent-error"></a>啟用使用者同意錯誤

您的 Azure AD 租使用者系統管理員可能會制定原則，以防止租使用者使用者同意應用程式。 當使用者嘗試登入您的應用程式時，這種情況會導致類似下面的錯誤：

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

您必須聯絡 Azure AD 系統管理員，為租使用者中的所有使用者授與同意，或為您的特定應用程式啟用使用者同意。

## <a name="next-steps"></a>後續步驟

* 如需支援的連接字串清單，請參閱 [Kusto 連接字串](kusto/api/connection-strings/kusto.md) 。
