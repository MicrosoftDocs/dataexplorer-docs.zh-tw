---
title: Kusto 存取控制概觀 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 Kusto 存取控制概觀。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 9d72373e8fc5c55740fa3e53a8f850a887517e7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490441"
---
# <a name="kusto-access-control-overview"></a>Kusto 存取控制概觀

Kusto 中的存取控制是以兩個維度為基礎：
* [驗證](#authentication)：驗證提出要求之安全性主體的身分識別
* [授權](#authorization)：允許驗證提出要求的安全性主體，以在目標資源上提出該要求

為了在 Kusto 叢集、資料庫或資料表上成功執行查詢或控制命令，動作項目必須成功通過驗證與授權。

## <a name="authentication"></a>驗證


**Azure Active Directory (AAD)** 是 Azure 慣用的多租用戶雲端目錄服務，能夠驗證安全性主體或與其他識別提供者 (例如 Microsoft 的 Active Directory) 同盟。

AAD 是在 Microsoft 中向 Kusto 驗證的慣用方法。 其支援數種驗證案例：
* **使用者驗證** (互動式登入)：用來驗證人為主體。
* **應用程式驗證 (非互動式登入)** ：用來驗證必須執行/驗證而不存在任何人為使用者的服務和應用程式。 

### <a name="user-authentication"></a>使用者驗證
使用者驗證會在使用者向 AAD 出示認證 (或向某些與 AAD 搭配使用的識別提供者，例如 ADFS) 時進行，並收到可向 Kusto 服務出示的安全性權杖。 Kusto 服務並不在意安全性權杖的取得方式，它會在意權杖是否有效，以及 AAD (或同盟 IdP) 所放入的資訊。

在用戶端上，Kusto 支援互動式驗證，其中 AAD 用戶端程式庫 ADAL 或類似的程式碼會要求使用者輸入認證。 它也支援以權杖為基礎的驗證，其中使用 Kusto 的應用程式會取得有效的使用者權杖並予以出示。 最後，它可支援使用 Kusto 的應用程式為其他一些服務 (而非 Kusto) 取得有效的使用者權杖，前提是該資源與 Kusto 之間有信任關係。

如需有關如何使用 Kusto 用戶端程式庫，以及使用 AAD 向 Kusto 進行驗證的詳細資訊，請參閱 [Kusto 連接字串](../../api/connection-strings/kusto.md)。

### <a name="application-authentication"></a>應用程式驗證
若要求並未與特定使用者相關聯，或沒有可供輸入認證的使用者，則可能使用 AAD 應用程式驗證流程。 在此流程中，應用程式會藉由出示一些秘密資訊來向 AAD (或同盟 IdP) 進行驗證。 各種 Kusto 用戶端都支援下列案例：

* 使用本機安裝的 X.509v2 憑證進行應用程式驗證。
* 使用提供給用戶端程式庫的 X.509v2 憑證作為位元組資料流，進行應用程式驗證。
* 使用 AAD 應用程式識別碼和 AAD 應用程式金鑰 (相當於應用程式的使用者名稱/密碼驗證) 進行應用程式驗證。
* 使用先前取得的有效 AAD 權杖 (發行至 Kusto) 進行應用程式驗證。
* 使用先前取得並發行給其他資源的有效 AAD 權杖進行應用程式驗證，前提是該資源與 Kusto 之間有信任關係。


### <a name="microsoft-accounts-msas"></a>Microsoft 帳戶 (MSA)
Microsoft 帳戶 (MSA) 一詞用於所有 Microsoft 管理的非組織使用者帳戶 (例如 `hotmail.com`、`live.com`、`outlook.com`)。
Kusto 支援 MSM 的使用者驗證 (請注意，不具安全性群組概念)，這是由其 UPN (通用主體名稱) 所識別。
在 Kusto 資源上設定 MSA 主體時，Kusto **不會**嘗試解析所提供的 UPN。

### <a name="authenticated-sdk-or-rest-calls"></a>已驗證的 SDK 或 REST 呼叫
* 使用 REST API 時，系統會使用標準 HTTP `Authorization` 標頭來執行驗證。
* 使用任何 Kusto .NET 程式庫時，驗證的控制方式是在 [Kusto 連接字串](../../api/connection-strings/kusto.md)中指定驗證方法和參數，或在[用戶端要求屬性](https://kusto.azurewebsites.net/docs/api/request-properties.html)物件上設定屬性。

### <a name="kusto-client-sdk-as-an-aad-client-application"></a>Kusto 用戶端 SDK 作為 AAD 用戶端應用程式
當 Kusto 用戶端程式庫叫用 ADAL (AAD 用戶端程式庫) 以取得與 Kusto 通訊的權杖時，它會提供下列資訊：

1. 資源 (叢集 URI，例如 `https://Cluster-and-region.kusto.windows.net`)
2. AAD 用戶端應用程式識別碼
3. AAD 用戶端應用程式重新導向 URI
4. AAD 租用戶 (這會影響用於驗證的 AAD 端點，例如，對於 AAD 租用戶 `microsoft.com`，AAD 端點會是 `https://login.microsoftonline.com/microsoft.com`)

ADAL 傳回給 Kusto 用戶端程式庫的權杖會以適當的 Kusto 叢集 URL 作為受眾，並以「存取 Kusto」權限作為範圍。

**範例：取得 Kusto 叢集的 AAD 使用者權杖**
```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD TenantID or name}");

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

所有已驗證的主體 (不論用於驗證的方法為何) 也會先經過授權檢查，才能夠在 Kusto 資源上執行動作。
Kusto 會使用[以角色為基礎的授權模型](role-based-authorization.md)：主體會歸屬於一或多個**安全性角色**，而只要其中一個主體的角色獲得授權，授權就會成功。

例如，**資料庫使用者角色**會授與安全性主體 (使用者或服務) 讀取特定資料庫資料、在資料庫中建立資料表，以及在其中建立函式的許可權。

您可個別地定義安全性主體與安全性角色的關聯，或使用安全性群組 (定義於 AAD) 進行定義。 執行此作業的個別命令定義於[設定以角色為基礎的授權規則](../security-roles.md)。

