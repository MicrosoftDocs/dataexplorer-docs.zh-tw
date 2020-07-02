---
title: Kusto 存取控制概觀 - Azure 資料總管
description: 本文說明 Azure 資料總管中的 Kusto 存取控制概觀。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 11/25/2019
ms.openlocfilehash: 7031ecf15ea3f7a472fbfbe1791d166e2e35b065
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763890"
---
# <a name="kusto-access-control-overview"></a>Kusto 存取控制概觀

Azure 資料總管中的存取控制是以兩個關鍵因素為基礎。
* [驗證](#authentication)：驗證提出要求之安全性主體的身分識別
* [授權](#authorization)：允許驗證提出要求的安全性主體，以在目標資源上提出該要求

Azure 資料總管叢集、資料庫或資料表上的查詢或控制命令必須通過驗證和授權檢查。

## <a name="authentication"></a>驗證

**Azure Active Directory (Azure AD)** 是 Azure 慣用的多租用戶雲端目錄服務。 其可驗證安全性主體，或與其他識別提供者同盟。

Azure AD 是在 Microsoft 中向 Azure 資料總管驗證的慣用方法。 其支援數種驗證案例。
* **使用者驗證** (互動式登入)：用來驗證人為主體。
* **應用程式驗證 (非互動式登入)** ：用來驗證必須執行和驗證而不存在任何人類使用者的服務和應用程式。

### <a name="user-authentication"></a>使用者驗證

使用者驗證是在使用者出示認證時進行：
* Azure AD 
* 搭配 Azure AD 運作的識別提供者

如果成功，使用者會收到可向 Azure 資料總管服務出示的安全性權杖。 Azure 資料總管服務並不在意如何取得安全性權杖。 但是會關切權杖是否有效，以及 Azure AD (或同盟 IdP) 所放入的資訊。

在用戶端上，Azure 資料總管支援互動式驗證，其中 Microsoft 驗證程式庫或類似的程式碼會要求使用者輸入認證。 此外，也支援權杖型驗證，其中使用 Azure 資料總管的應用程式會取得有效的使用者權杖。 使用 Azure 資料總管的應用程式也可以取得另一項服務的有效使用者權杖。 只有在該資源與 Azure 資料總管之間存在信任關係時，才可取得使用者權杖。

如需詳細資訊，請參閱 [Kusto 連接字串](../../api/connection-strings/kusto.md)，以取得有關如何使用 Kusto 用戶端程式庫，以及使用 Azure AD 向 Azure 資料總管進行驗證的詳細資訊。

### <a name="application-authentication"></a>應用程式驗證

若要求並未與特定使用者相關聯，或沒有可供輸入認證的使用者，請使用 Azure AD 應用程式驗證流程。 在此流程中，應用程式會藉由出示一些秘密資訊來向 Azure AD (或同盟 IdP) 進行驗證。 各種 Azure 資料總管用戶端都支援下列案例。

* 使用本機安裝的 X.509v2 憑證進行應用程式驗證
* 使用提供給用戶端程式庫的 X.509v2 憑證作為位元組資料流，進行應用程式驗證
* 使用 Azure AD 應用程式識別碼和 Azure AD 應用程式金鑰進行應用程式驗證。

    > [!NOTE] 
    > 識別碼和金鑰等同於使用者名稱和密碼

* 使用先前取得的有效 Azure AD 權杖 (發行至 Azure 資料總管) 進行應用程式驗證。
* 使用先前取得的有效 Azure AD 權杖 (發行至其他資源) 進行應用程式驗證。 如果該資源與 Azure 資料總管之間有信任關係，此方法就會可行。

### <a name="microsoft-accounts-msas"></a>Microsoft 帳戶 (MSA)

Microsoft 帳戶 (MSA) 一詞用於所有 Microsoft 管理的非組織使用者帳戶 (例如 `hotmail.com`、`live.com`、`outlook.com`)。
Kusto 支援 MSM 的使用者驗證 (不具安全性群組概念)，這是由其通用主體名稱 (UPN) 所識別。

若已在 Azure 資料總管資源上設定 MSA 主體，則 Azure 資料總管**不會**嘗試解析所提供的 UPN。

### <a name="authenticated-sdk-or-rest-calls"></a>已驗證的 SDK 或 REST 呼叫

* 使用 REST API 時，系統會使用標準 HTTP `Authorization` 標頭來進行驗證。
* 使用任何 Azure 資料總管 .NET 程式庫時，驗證的控制方式是在[連接字串](../../api/connection-strings/kusto.md)中指定驗證方法和參數。 另一個方法是在[用戶端要求屬性](../../api/netfx/request-properties.md)物件上設定屬性。

### <a name="azure-data-explorer-client-sdk-as-an-azure-ad-client-application"></a>Azure 資料總管用戶端 SDK 即 Azure AD 用戶端應用程式

當 Kusto 用戶端程式庫叫用 Microsoft 驗證程式庫以取得與 Kusto 通訊的權杖時，其會提供下列資訊：

* 資源 (叢集 URI，例如 `https://Cluster-and-region.kusto.windows.net`)
* Azure AD 用戶端應用程式識別碼
* Azure AD 用戶端應用程式重新導向 URI
* Azure AD 租用戶會影響用於驗證的 Azure AD 端點。 例如，對於 Azure AD 租用戶 `microsoft.com` 而言，Azure AD 端點為 `https://login.microsoftonline.com/microsoft.com`。

Microsoft 驗證程式庫傳回給 Azure 資料總管用戶端程式庫的權杖，具有適當的 Azure 資料總管叢集 URL 作為物件，並以「存取 Azure 資料總管」權限作為範圍。

**範例：取得 Azure 資料總管叢集的 Azure AD 使用者權杖**

```csharp
// Create Auth Context for Azure AD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD TenantID or name}");

// Provide your Application ID and redirect URI
var clientAppID = "{your client app id}";
var redirectUri = new Uri("{your client app redirect uri}");

// acquireTokenTask will receive the bearer token for the authenticated user
var acquireTokenTask = authContext.AcquireTokenAsync(
    $"https://{clusterNameAndRegion}.kusto.windows.net",
    clientAppID,
    redirectUri,
    new PlatformParameters(PromptBehavior.Auto, null)).GetAwaiter().GetResult();
```

## <a name="authorization"></a>授權

所有經過驗證的主體都會先進行授權檢查，才能在 Azure 資料總管資源上執行動作。
Azure 資料總管會使用[角色型授權模型](role-based-authorization.md)，其中的主體會歸屬於一個或多個安全性角色。 只要其中一個主體的角色獲得授權，授權就會成功。

例如，資料庫使用者角色會授與安全性主體、使用者或服務以下權限：
* 讀取特定資料庫的資料
* 在資料庫中建立資料表
* 在資料庫中建立函式

您可個別地定義安全性主體與安全性角色的關聯，或使用安全性群組 (定義於 Azure AD) 進行定義。 [設定角色型授權規則](../security-roles.md)中會定義相關命令。
