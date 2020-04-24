---
title: 如何 - 建立 AAD 應用程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹如何 - 在 Azure 資料資源管理員中創建 AAD 應用程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 91d646d3bd0922f9daf9e8caec6fa6be5e32e2b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522894"
---
# <a name="howto-creating-an-aad-application"></a>如何:建立 AAD 應用程式

雖然 AAD 使用者身份驗證非常簡單(因為租戶管理員(如 MSIT)在 AAD 中定義使用者),但 AAD 應用程式身份驗證要複雜一些。 這是因為它需要您創建應用程式並將其註冊到 AAD。 此過程如下所述,包括一些詳細資訊。

AAD 應用程式身份驗證對於需要存取 Kusto 而不允許使用者登錄或存在的應用程式(例如,無人值守的服務或計劃流)非常有用。

具有互動式使用者上下文的用戶端應用程式和中間層應用程式應避免使用此模型。 這是因為授權是基於 AAD 應用程式標識而不是使用者標識執行的,因此調用應用程式需要實現自己的授權邏輯以防止誤用。

## <a name="application-authentication-use-cases"></a>應用程式識別驗證案例

有兩種使用應用程式身份驗證的主要方案:
* 直接代表自己與 Kusto 服務聯絡的應用程式
* 將對其使用者進行 Kusto 認證的應用程式 (委派身份認證)

## <a name="1-provisioning-a-new-application"></a>1. 預先使用的應用程式

#### <a name="register-the-new-application"></a>註冊新應用程式

1. 登錄到[Azure 門戶](https://portal.azure.com)並打開`Azure Active Directory`邊欄選項卡。

    ![alt 文字](./images/Aad-create-app-step-0.png "Aad-創建-應用步驟-0")

1. 選擇`App registrations`刀片和裝刀時,`New application registration`選擇 。

    ![alt 文字](./images/Aad-create-app-step-1.png "Aad-創建-應用步驟-1")

1. 填寫申請詳細資訊:
    * 名稱
    * 類型:設定為`Web app/API`
    * 登錄網址:使用者用於存取應用程式的網址。 AAD 不驗證 URL,<br>
        但強制要求您提供一個值。 因此,如果應用程式沒有任何用戶可以訪問的 URL,<br>
        輸入屬於應用程式的 URL(例如 HTTPs://<APP-CNAME> 或 HTTPs://<云服务-NAME>.cloudapp.net)。<br>
        您甚至可以提供值,如果尚未寫入應用程式,則可以繼續。

    ![alt 文字](./images/Aad-create-app-step-2.png "Aad-創建-應用步驟-2")

1. 應用程式準備就緒后,打開其`Settings`刀片。

    ![alt 文字](./images/Aad-create-app-step-3.png "Aad-創建-應用步驟-3")

1. 在邊`Keys`欄選項卡上,為新應用程式設定身份驗證:
    * 如果要使用共享金鑰,請從下拉菜單中選擇密鑰持續時間,並在生成密鑰時複製該密鑰。
        您將無法還原此金鑰。
    * 或者,使用 X509 證書對應用程式進行身份驗證。
        為此,請選擇`Upload Public Key`並按照說明上載證書的公共部分。
    * 不要忘記選擇`Save`何時完成。

    ![alt 文字](./images/Aad-create-app-step-4.png "Aad-創建-應用步驟-4")

您的應用程式已設置。 如果只需使用新創建的應用程式訪問 Kusto,您就完成了。

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>為 Kusto 服務應用程式設定委派權限

如果需要應用程式能夠對 Kusto 存取執行使用者或應用程式驗證,則需要在應用程式和 Kusto 之間設定信任:

1. 在邊`Required permissions`列選項卡上`Add`, 選擇 。

    ![alt 文字](./images/Aad-create-app-step-5.png "Aad-創建-應用步驟-5")

1. 選擇`Select an API`,`KustoService`在 篩選器框中輸入`KustoService (Kusto)`並選擇 。
1. 如果您的 Kusto 叢集需要 MFA,請在篩選器`KustoMFA`框`KustoServiceMFA (KustoMFA)`中輸入 並選擇 。

    ![alt 文字](./images/Aad-create-app-step-6.png "Aad-創建-應用步驟-6")

1. 確認所選內容後,為選擇`Access Kusto`委派的許可權。

    ![alt 文字](./images/Aad-create-app-step-7.png "Aad-建立-應用步驟7")

1. 選擇`Done`以完成該過程。



### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. 在 Kusto 叢集上設定應用程式的權限

> 在使用新預配的應用程式之前,請驗證它從圖形 API 解析。<br>
    AAD 更改最多可能需要幾個小時才能傳播。

1. 訪問資料庫管理員以向新應用授予許可權。
有關設置許可權的詳細資訊,請查看[管理資料庫主體](../security-roles.md)部分。<br>
有關設定引入權限,請參閱[引入權限](../../api/netfx/kusto-ingest-client-permissions.md)。
1. 如果尚未連接到庫斯特資源管理器中此服務,請添加連接。

### <a name="3-application-can-now-access-kusto"></a>3. 應用程式現在可以存取庫托

使用新預先使用的應用程式使用 Kusto 客戶端庫存取 Kusto 叢集時,請指定以下連接字串(如果您選擇使用共享金鑰進行身份驗證):

`https://`*叢集Dns名稱*`/;Federated Security=True;Application Client Id=`*應用程式客戶端應用程式*`;Application Key=`*金鑰*


### <a name="appendix-a-aad-errors"></a>附錄 A:AAD 錯誤

#### <a name="aad-error-aadsts650057"></a>AAD 錯誤 AADSS650057

如果應用程式用於驗證使用者或應用程式對 Kusto 訪問,則必須為 Kusto 服務應用程式設置委派的許可權,即聲明您的應用程式可以驗證使用者或應用程式進行 Kusto 訪問。
如果不這樣做,在進行身份驗證嘗試時,將導致類似於以下內容的錯誤:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

您需要按照有關[為 Kusto 服務應用程式設定委派許可權的說明](#set-up-delegated-permissions-for-kusto-service-application)進行操作。

#### <a name="enable-user-consent-if-needed"></a>根據需要啟用使用者同意

您的 AAD 租戶管理員可能會制定一項策略,以防止租戶使用者同意應用程式。 當使用者嘗試登入您的應用程式時,這將導致類似於以下內容的錯誤:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

您需要要求 AAD 管理員為租戶中的所有使用者授予同意,或為您的特定應用程式啟用使用者同意。



### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>附錄 B:進階主題和故障排除

* 有關支援的連接字串的完整參考,請參閱[Kusto 連接字串](../../api/connection-strings/kusto.md)文件
