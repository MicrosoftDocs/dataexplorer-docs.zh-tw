---
title: Kusto 使用 AAD 進行驗證以進行存取-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中使用 AAD for Azure 資料總管存取進行驗證。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 09/13/2019
ms.openlocfilehash: f74848ac3b634affbafde8d0441a4340aff230da
ms.sourcegitcommit: dc42f4a7fa617a06b5566ce40b7cdc66cfd22185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87557613"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>如何使用適用于 Azure 資料總管存取的 AAD 進行驗證

若要存取 Azure 資料總管，建議的方法是向**Azure Active Directory**服務進行驗證 (有時也稱為**Azure AD**，或只是**AAD**) 。 這麼做可確保 Azure 資料總管永遠不會看到存取主體的目錄認證，方法是使用兩個階段的進程：

1. 在第一個步驟中，用戶端會與 AAD 服務進行通訊、對其進行驗證，並要求針對用戶端想要存取的特定 Azure 資料總管端點所發行的存取權杖。
2. 在第二個步驟中，用戶端會發出對 Azure 資料總管的要求，並提供第一個步驟取得的存取權杖，做為 Azure 資料總管的身分識別證明。

接著，Azure 資料總管會代表 AAD 發出存取權杖的安全性主體來執行要求，並使用此身分識別來執行所有授權檢查。

在大部分情況下，建議使用其中一個 Azure 資料總管 Sdk 以程式設計方式存取服務，因為這會移除執行 (上述流程的許多麻煩，而其他) 則不容易。 例如，請參閱[.NET SDK](../../api/netfx/about-the-sdk.md)。
然後， [Kusto 連接字串](../../api/connection-strings/kusto.md)會設定驗證屬性。
如果無法這麼做，請繼續閱讀，以取得如何自行執行此流程的詳細資訊。

主要的驗證案例包括：

* **驗證已登入使用者的用戶端應用程式**。
  在此案例中，互動式 (用戶端) 應用程式會對使用者觸發 AAD 提示， (例如使用者名稱和密碼) 。
  請參閱[使用者驗證](#user-authentication)、

* 「無**外設」應用程式**。
  在此案例中，應用程式是在執行，但沒有使用者提供認證，而是應用程式會使用已設定的某些認證，以「本身」身分向 AAD 進行驗證。
  請參閱[應用程式驗證](#application-authentication)。

* 代理者**驗證**。
  在此案例中，有時稱為「web 服務」或「web 應用程式」案例，應用程式會從另一個應用程式取得 AAD 存取權杖，然後將它轉換成可搭配 Azure 資料總管使用的另一個 AAD 存取權杖。
  換句話說，應用程式會在提供認證和 Azure 資料總管服務的使用者或應用程式之間做為中繼程式。
  請參閱代理者[驗證](#on-behalf-of-authentication)。

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>指定 Azure 資料總管的 AAD 資源

從 AAD 取得存取權杖時，用戶端必須告知 AAD 應將權杖發行至哪個**aad 資源**。 Azure 資料總管端點的 AAD 資源是端點的 URI，並不會有埠資訊和路徑。 例如：

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>指定 AAD 租使用者識別碼

AAD 是多租使用者服務，而且每個組織都可以在 AAD 中建立名為**directory**的物件。 目錄物件會保存安全性相關的物件，例如使用者帳戶、應用程式和群組。 AAD 通常會以**租**使用者的身分來參考該目錄。 AAD 租使用者會以 GUID (**租使用者識別碼**) 來識別。 在許多情況下，也可以透過組織的功能變數名稱來識別 AAD 租使用者。

例如，名為 "Contoso" 的組織可能會有租使用者識別碼 `4da81d62-e0a8-4899-adad-4349ca6bfe24` 和功能變數名稱 `contoso.com` 。

## <a name="specifying-the-aad-authority"></a>指定 AAD 授權單位

AAD 有數個用於驗證的端點：

* 當知道要驗證之主體的租使用者時 (也就是，當其中一個人知道哪些 AAD 目錄中的使用者或應用程式) 時，AAD 端點就是 `https://login.microsoftonline.com/{tenantId}` 。
  在這裡， `{tenantId}` 是 AAD 中組織的租使用者識別碼，或其功能變數名稱 (例如 `contoso.com`) 。

* 當裝載所要驗證之主體的租使用者未知時，可以使用「通用」端點，將上面的取代 `{tenantId}` 為值 `common` 。

> [!NOTE]
> 用於驗證的 AAD 端點也稱為**aad 授權單位 URL**或單純的**aad 授權**單位。

## <a name="aad-token-cache"></a>AAD 權杖快取

使用 Azure 資料總管 SDK 時，AAD 權杖會儲存在本機電腦的每個使用者權杖快取中 (名為 **%APPDATA%\Kusto\tokenCache.data**的檔案，該檔案只能由登入的使用者存取或解密。 ) 在提示使用者提供認證之前檢查權杖的快取，進而大幅減少使用者必須輸入認證的次數。

> [!NOTE]
> AAD 權杖快取會減少使用者在存取 Azure 資料總管時所顯示的互動式提示數目，但不會降低其完成。 此外，當使用者收到認證的提示時，將無法預先預期。
> 這表示如果需要支援非互動式登入 () 例如在非互動式登入下執行時，如果提示登入的使用者有認證，則不一定要嘗試使用使用者帳戶來存取 Azure 資料總管，因為這種情況是因為時間所發出，而不是在系統提示時，將會失敗。


## <a name="user-authentication"></a>使用者驗證

以使用者驗證存取 Azure 資料總管最簡單的方式是使用 Azure 資料總管 SDK，並將 `Federated Authentication` azure 資料總管連接字串的屬性設定為 `true` 。 第一次使用 SDK 將要求傳送至服務時，使用者會看到登入表單以輸入 AAD 認證，而在驗證成功時，將會傳送要求。

不使用 Azure 資料總管 SDK 的應用程式仍可使用 AAD 用戶端程式庫 (ADAL) ，而不是執行 AAD 服務安全性通訊協定用戶端。 如需 https://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet 從 .net 應用程式執行這項操作的範例，請參閱 []。

若要驗證 Azure 資料總管存取的使用者，必須先將委派的許可權授與應用程式 `Access Kusto` 。 如需詳細資訊，請參閱[AAD 應用程式](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application)布建的 Kusto 指南。

下列簡短的程式碼片段示範如何使用 ADAL 來取得 AAD 使用者權杖，以存取 Azure 資料總管 (啟動登入 UI) ：

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire user token for the interactive user for Kusto:
AuthenticationResult result = authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", "your client app id",
    new Uri("your client app resource id"), new PlatformParameters(PromptBehavior.Auto)).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="application-authentication"></a>應用程式驗證

下列簡短的程式碼片段示範如何使用 ADAL 來取得 AAD 應用程式權杖，以存取 Azure 資料總管。 在此流程中，不會顯示任何提示，而且應用程式必須向 AAD 註冊，並配備執行應用程式驗證所需的認證 (例如 AAD 所發行的應用程式金鑰，或已預先向 AAD) 註冊的 X509v2 憑證。

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire application token for Kusto:
ClientCredential applicationCredentials = new ClientCredential("your application client ID", "your application key");
AuthenticationResult result =
        authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", applicationCredentials).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="on-behalf-of-authentication"></a>代理者驗證

在此案例中，應用程式已針對應用程式所管理的部分任意資源傳送 AAD 存取權杖，並使用該權杖來取得 Azure 資料總管資源的新 AAD 存取權杖，讓應用程式可以代表原始 AAD 存取權杖所指示的主體來存取 Kusto。

此流程稱為[OAuth2 token 交換流程](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04)。
通常需要使用 AAD 的多個設定步驟，而在某些情況下 (視 AAD 租使用者設定而定) 可能需要 AAD 租使用者的系統管理員進行特殊同意。



**步驟1：在您的應用程式與 Azure 資料總管服務之間建立信任關係**

1. 開啟[Azure 入口網站](https://portal.azure.com/)，並確定您已登入正確的租使用者 (查看用來登入入口網站) 身分識別的右上角。

2. 在 [資源] 窗格中，按一下 [ **Azure Active Directory**]，然後**應用程式註冊**]。

3. 找出使用代理者流程的應用程式，並加以開啟。

4. 依序按一下 [ **API 許可權**] 和 [**新增許可權**]。

5. 搜尋名為**Azure 資料總管**的應用程式，並加以選取。

6. 選取 [ **user_impersonation/存取 Kusto**]。

7. 按一下 [**新增許可權**]。

**步驟2：在您的伺服器程式碼中執行權杖交換**

```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Exchange your token for a Kusto token.
// You will need to provide your application's client ID and secret to authenticate your application
var tokenForKusto = authContext.AcquireTokenAsync(
    "https://{serviceNameAndRegion}.kusto.windows.net",
    new ClientCredential(customerAadWebApplicationClientId, customerAAdWebApplicationSecret),
    new UserAssertion(customerAadWebApplicationToken)).GetAwaiter().GetResult();
```

**步驟3：將權杖提供給 Kusto 用戶端程式庫並執行查詢**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}",
    clusterName,
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>Web 用戶端 (JavaScript) 驗證和授權



**AAD 應用程式設定**

> [!NOTE]
> 除了設定 AAD 應用程式所需遵循的標準[步驟](./how-to-provision-aad-app.md)之外，您也應該在 aad 應用程式中啟用 oauth 隱含流程。 若要達到此目的，您可以從 azure 入口網站的應用程式頁面中選取 [資訊清單]，並將 oauth2AllowImplicitFlow 設定為 [true]。

**詳細資料**

當用戶端是在使用者的瀏覽器中執行的 JavaScript 程式碼時，會使用隱含授與流程。 授與用戶端應用程式存取權給 Azure 資料總管服務的權杖，會在 URI 片段中的重新導向 URI (的一部分之後立即提供) ;此流程中未提供任何重新整理權杖，因此用戶端無法長時間快取權杖，並加以重複使用。

就像在 native client 流程中， (伺服器和用戶端) 都應該有兩個 AAD 應用程式，且兩者之間已設定關聯性。

AdalJs 需要在進行任何 access_token 呼叫之前取得 id_token。

存取權杖是藉由呼叫 `AuthenticationContext.login()` 方法取得，而 access_tokens 是藉由呼叫取得 `Authenticationcontext.acquireToken()` 。

* 建立具有正確設定的 AuthenticationCoNtext：

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* `authContext.login()` `acquireToken()` 如果您未登入，請在嘗試之前呼叫。 如果您登入或不是要知道) 它是否傳回，是一個很好的方法。 `authContext.getCachedUser()` `false`
* `authContext.handleWindowCallback()`每當您的頁面載入時呼叫。 這段程式碼會攔截來自 AAD 的重新導向，並從片段 URL 提取權杖並加以快取。
* 呼叫 `authContext.acquireToken()` 以取得實際的存取權杖，現在您已經有有效的識別碼權杖。 AcquireToken 的第一個參數將會是 Kusto 伺服器 AAD 應用程式資源 URL。

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* 在 callbackThatUsesTheToken 中，您可以使用權杖做為 Azure 資料總管要求中的持有人權杖。 例如：

```javascript
var settings = {
    url: "https://" + clusterAndRegionName + ".kusto.windows.net/v1/rest/query",
    type: "POST",
    data: JSON.stringify({
        "db": dbName,
        "csl": query,
        "properties": null
    }),
    contentType: "application/json; charset=utf-8",
    headers: { "Authorization": "Bearer " + token },
    dataType: "json",
    jsonp: false,
    success: function(data, textStatus, jqXHR) {
        if (successCallback !== undefined) {
            successCallback(data.Tables[0]);
        }

    },
    error: function(jqXHR, textStatus, errorThrown) {
        if (failureCallback !==  undefined) {
            failureCallback(textStatus, errorThrown, jqXHR.responseText);
        }

    },
};

$.ajax(settings).then(function(data) {/* do something wil the data */});
```

> 警告-如果您在驗證時收到下列或類似的例外狀況：`ReferenceError: AuthenticationContext is not defined`
這可能是因為您在全域命名空間中沒有 AuthenticationCoNtext。
不幸的是，AdalJS 目前有未記載的需求，驗證內容將會在全域命名空間中定義。
