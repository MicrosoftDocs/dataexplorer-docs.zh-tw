---
title: 做法-建立 AAD 應用程式-Azure 資料總管 |Microsoft Docs
description: 本文說明如何做法在 Azure 資料總管中建立 AAD 應用程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 0816d55cda6d29820084514b0bfec27d726693f9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617964"
---
# <a name="howto-creating-an-aad-application"></a>做法：建立 AAD 應用程式

雖然 AAD 使用者驗證非常簡單（因為租使用者系統管理員已在 AAD 中定義使用者，例如 MSIT），AAD 應用程式驗證會變得更複雜。 這是因為它需要您使用 AAD 來建立及註冊應用程式。 下列詳細資料會說明此程式。

AAD 應用程式驗證適用于需要存取 Kusto 的應用程式，而使用者不需登入或存在（例如，自動服務或排程的流程）。

具有互動式使用者內容的用戶端應用程式和中介層應用程式應該避免此模型。 這是因為授權是根據 AAD 應用程式識別來執行，而不是使用者身分識別，因此呼叫的應用程式將需要執行自己的授權邏輯，以避免誤用。

## <a name="application-authentication-use-cases"></a>應用程式驗證使用案例

有兩個主要的案例會使用應用程式驗證：
* 直接與 Kusto 服務連線的應用程式代表自己
* 將驗證其使用者以 Kusto 的應用程式（委派的驗證）

## <a name="1-provisioning-a-new-application"></a>1. 布建新的應用程式

#### <a name="register-the-new-application"></a>註冊新的應用程式

1. 登入[Azure 入口網站](https://portal.azure.com)並開啟`Azure Active Directory`分頁。

    :::image type="content" source="images/aad-create-app-step-0.png" alt-text="Aad 建立應用程式步驟0":::

1. 選擇`App registrations` [當] 分頁載入時， `New application registration`選取 []。

    :::image type="content" source="images/aad-create-app-step-1.png" alt-text="Aad 建立應用程式步驟1":::

1. 填入應用程式詳細資料：
    * 名稱
    * 類型：設定為`Web app/API`
    * [登入 URL]：使用者用來存取應用程式的 URL。 AAD 不會驗證 URL，<br>
        但是，系統會要求您提供值。 因此，如果應用程式沒有任何使用者可存取的 URL，<br>
        輸入屬於應用程式的 URL （例如 HTTPs://<APP-CNAME> 或 HTTPs://<雲端服務名稱>. cloudapp.net）。<br>
        您甚至可以提供值，並在應用程式尚未寫入時繼續進行。

    
    :::image type="content" source="images/aad-create-app-step-2.png" alt-text="Aad 建立應用程式步驟2":::

1. 一旦您的應用程式準備就緒， `Settings`請開啟其分頁。

    :::image type="content" source="images/aad-create-app-step-3.png" alt-text="Aad 建立步驟3":::

1. 在 [ `Keys` ] 分頁上，為新的應用程式設定驗證：
    * 如果您想要使用共用金鑰，請從下拉式功能表中選取 [金鑰持續時間]，並在產生金鑰時加以複製。
        您將無法再還原此金鑰。
    * 或者，使用 X509 憑證來驗證您的應用程式。
        若要這麼做， `Upload Public Key`請選取並遵循指示來上傳憑證的公開部分。
    * 當您完成時`Save` ，別忘了選取。

    :::image type="content" source="images/aad-create-app-step-4.png" alt-text="Aad 建立應用程式步驟4":::

您的應用程式已設定完成。 如果您只需要使用新建立的應用程式來存取 Kusto，就大功告成了。

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>設定 Kusto 服務應用程式的委派許可權

如果您需要應用程式能夠執行使用者或應用程式驗證以進行 Kusto 存取，您必須設定應用程式與 Kusto 之間的信任：

1. 在 [ `Required permissions` ] 分頁上`Add`，選取 []。

    :::image type="content" source="images/aad-create-app-step-5.png" alt-text="Aad 建立應用程式步驟5":::
   
1. 選擇`Select an API`[] `KustoService` ，在 [篩選] 方塊`KustoService (Kusto)`中輸入，然後選取。
1. 如果您的 Kusto 叢集需要 MFA， `KustoMFA`請在 [篩選] 方塊`KustoServiceMFA (KustoMFA)`中輸入，然後選取。

    :::image type="content" source="images/aad-create-app-step-6.png" alt-text="Aad 建立應用程式步驟6":::

1. 確認您的選擇之後，請選擇 [ `Access Kusto`委派的許可權]。

   :::image type="content" source="images/aad-create-app-step-7.png" alt-text="Aad 建立應用程式步驟7":::

1. 選取`Done`以完成此程式。


### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. 在 Kusto 叢集上設定應用程式的許可權

> 使用新布建的應用程式之前，請先確認它會從圖形 API 解析。<br>
    最多可能需要數小時的時間，AAD 變更才會傳播。

1. 存取您的資料庫管理員，將許可權授與新的應用程式。
如需設定許可權的詳細資訊，請參閱[管理資料庫主體](../security-roles.md)一節。<br>
如需設定內嵌許可權，請參閱內嵌[許可權](../../api/netfx/kusto-ingest-client-permissions.md)。
1. 如果您還沒有此服務的連線，請在您的 Kusto Explorer 中加入此服務的連接。

### <a name="3-application-can-now-access-kusto"></a>3. 應用程式現在可以存取 Kusto

當您使用新布建的應用程式來使用 Kusto 用戶端程式庫來存取 Kusto 叢集時，請指定下列連接字串（如果您選擇使用共用金鑰進行驗證）：

`https://`*ClusterDnsName*`/;Federated Security=True;Application Client Id=`*ApplicationClientId*ApplicationClientId`;Application Key=`*ApplicationKey*


### <a name="appendix-a-aad-errors"></a>附錄 A： AAD 錯誤

#### <a name="aad-error-aadsts650057"></a>AAD 錯誤 AADSTS650057

如果您的應用程式是用來驗證使用者或應用程式以進行 Kusto 存取，您就必須設定 Kusto 服務應用程式的委派許可權，也就是宣告您的應用程式可以驗證使用者或應用程式以進行 Kusto 存取。
當嘗試進行驗證時，不這麼做會導致類似下列的錯誤：

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

您必須遵循[設定 Kusto 服務應用程式的委派許可權](#set-up-delegated-permissions-for-kusto-service-application)中的指示。

#### <a name="enable-user-consent-if-needed"></a>視需要啟用使用者同意

您的 AAD 租使用者系統管理員可能會制定原則，以防止租使用者使用者同意應用程式。 當使用者嘗試登入您的應用程式時，這會產生類似下列的錯誤：

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

您必須要求 AAD 系統管理員為租使用者中的所有使用者授與同意，或為您的特定應用程式啟用使用者同意。

### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>附錄 B： Advanced 主題和疑難排解

* 如需所支援連接字串的完整參考，請參閱[Kusto 連接字串](../../api/connection-strings/kusto.md)檔
