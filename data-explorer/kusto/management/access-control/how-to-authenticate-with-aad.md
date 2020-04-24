---
title: Azure 資料資源管理員存取使用 AAD 進行身份驗證 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中 Azure 數據資源管理器訪問中的"如何使用 AAD 進行身份驗證」。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: dc3a87a290ac535ac475af5017170a0478cd9b54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522911"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>使用 AAD 進行 Azure 資料資源管理員存取的如何進行身份驗證

訪問 Azure 資料資源管理員的建議方法是對**Azure 活動目錄**服務進行身份驗證(有時也稱為 Azure **AD**,或者只是**AAD**)。 這樣做可確保 Azure 資料資源管理員永遠不會看到訪問主體的目錄認證,使用兩階段過程:

1. 在第一步中,用戶端與 AAD 服務通訊,對其進行身份驗證,並請求專門為用戶端打算訪問的特定 Azure 數據資源管理器終結點頒發的訪問權杖。
2. 在第二步中,用戶端向 Azure 數據資源管理員發出請求,提供在第一步中獲取的訪問權杖,作為 Azure 資料資源管理員的標識證明。

然後,Azure 數據資源管理員代表 AAD 為其發出訪問權杖的安全主體執行請求,並且所有授權檢查都使用此識別執行。

在大多數情況下,建議使用 Azure 數據資源管理器 SDK 之一以程式設計方式存取服務,因為它們消除了實現上述流(以及其他許多)的麻煩。 例如,請參閱[.NET SDK](../../api/netfx/about-the-sdk.md)。
然後[,Kusto 連接字串](../../api/connection-strings/kusto.md)設置身份驗證屬性。
如果無法這樣做,請繼續閱讀,瞭解有關如何自己實現此流的詳細資訊。

主要的身份驗證方案是:

* **驗證登入使用者的客戶端應用程式**。
  在這種情況下,互動式(用戶端)應用程式會觸發 AAD 提示用戶獲得憑據(如使用者名和密碼)。
  請參閱[使用者身份驗證](#user-authentication),

* **一個「無頭」 的應用程式**。
  在這種情況下,應用程式運行時沒有使用者提供憑據,而是應用程式使用已配置的某些憑據將身份驗證為 AAD 的"自身"
  請參考[應用程式身份認證](#application-authentication)。

* **代表認證**。
  在這種情況下,有時稱為"Web 服務"或"Web 應用"方案,應用程式從其他應用程式獲取 AAD 訪問權杖,然後將其「轉換為」另一個 AAD 訪問權杖,該權杖可與 Azure 資料資源管理器一起使用。
  換句話說,應用程式充當提供憑據的使用者或應用程式與 Azure 數據資源管理器服務之間的仲介。
  請參考[代表認證](#on-behalf-of-authentication)。

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>為 Azure 資料資源管理員指定 AAD 資源

從 AAD 取得存取權杖時,客戶端必須告訴 AAD 該權杖應頒發給哪個**AAD 資源**。 Azure 資料資源管理器終結點的 AAD 資源是終結點的 URI,不包括埠資訊和路徑。 例如：

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>指定 AAD 租戶識別碼

AAD 是一個多租戶服務,每個組織都可以在 AAD 中創建一個稱為**目錄**的物件。 目錄物件包含與安全相關的物件,如使用者帳戶、應用程式和組。 AAD 通常將目錄稱為**租戶**。 AAD 租戶由 GUID (**租戶 ID**) 標識。 在許多情況下,AAD 租戶也可以由組織的功能變數名稱標識。

例如,名為「Contoso」的組織可能具有租戶`4da81d62-e0a8-4899-adad-4349ca6bfe24`ID 和功能`contoso.com`變數名稱。

## <a name="specifying-the-aad-authority"></a>指定 AAD 權限

AAD 具有許多用於身份驗證的終結點:

* 當承載要身份驗證的主體的租戶已知時(換句話說,當知道使用者或應用程式位於哪個 AAD 目錄時),AAD`https://login.microsoftonline.com/{tenantId}`終結點為 。
  此處`{tenantId}`是 AAD 中的組織的租戶 ID 或`contoso.com`其功能變數名稱(例如 )。

* 當承載要驗證的主體的租戶不知道時,"公共"終結點可以通過`{tenantId}`將 上述終結點替換`common`為值 來使用。

> [!NOTE]
> 認證的 AAD 終結點也稱為**AAD 權限網址**或只是**AAD 權限**。

## <a name="aad-token-cache"></a>AAD 權杖快取

使用 Azure 資料資源管理員 SDK 時,AAD 權限儲存在本地電腦上,位於每個使用者的權杖緩存中(一個稱為 **%APPDATA%__Kusto_tokenCache.data**的檔案,只能由登錄使用者存取或解密。在提示用戶輸入認證之前,將檢查緩存是否存在權杖,從而大大減少使用者必須輸入憑據的次數。

> [!NOTE]
> AAD 權杖緩存減少了使用者在存取 Azure 資料資源管理器時呈現的互動式提示數,但不會減少這些提示的完成。 此外,用戶無法提前預測何時會提示他們輸入憑據。
> 這意味著,如果需要支援非互動式登錄(例如,安排任務時),則不得嘗試使用使用者帳戶訪問 Azure 數據資源管理器,因為當提示登錄使用者獲取憑據時,在非互動式登錄下運行時提示該憑據將失敗。


## <a name="user-authentication"></a>使用者驗證

使用使用者身份驗證存取 Azure 資料資源管理員的最簡單方法是使用 Azure 資料資源管理`Federated Authentication`員 SDK 並將 Azure 資料資源管理`true`器連接字串的屬性設定為 。 首次使用 SDK 向服務發送請求時,將向使用者顯示登入表單以輸入 AAD 認證,並在成功身份驗證時發送請求。

不使用 Azure 資料資源管理器 SDK 的應用程式仍可以使用 AAD 用戶端庫 (ADAL), 而不是實現 AAD 服務安全協定用戶端。 有關 .NET 應用程式執行此操作的範例,請參https://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet閱 * 。

要對使用者進行 Azure 資料資源管理員訪問許可權的身份驗證,必須`Access Kusto`首先授予 應用程式委派的許可權。 有關詳細資訊,請參閱[Kusto AAD 應用程式預配指南](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application)。

以下簡短的程式碼段展示使用 ADAL 取得 AAD 使用者權限存取 Azure 資料資源管理員(啟動登入 UI):

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

以下簡要代碼段演示使用 ADAL 獲取 AAD 應用程式權杖來造訪 Azure 資料資源管理員。 在此流中,不顯示任何提示,並且應用程式必須註冊到 AAD 並配備執行應用程式身份驗證所需的認證(例如 AAD 頒發的應用金鑰或已預先向 AAD 註冊的 X509v2 憑證)。

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

## <a name="on-behalf-of-authentication"></a>代表身分驗證

在這種情況下,應用程式被發送到一個 AAD 訪問權杖,用於應用程式管理的某些任意資源,並且它使用該權杖為 Azure 資料資源管理員資源獲取新的 AAD 存取權杖,以便應用程式可以代表原始 AAD 存取權杖指示的主體存取 Kusto。

此流稱為[OAuth2 權杖交換流](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04)。
它通常需要使用 AAD 執行多個配置步驟,在某些情況下(取決於 AAD 租戶配置)可能需要 AAD 租戶管理員的特別同意。



**第一步:在應用程式和 Azure 資料資源管理員服務之間建立信任關係**

1. 打開[Azure 門戶](https://portal.azure.com/)並確保已登錄到正確的租戶(有關用於登錄到門戶的標識,請參閱右上角)。

2. 在資源窗格中,按下**Azure 的動作目錄**,然後按下**套用註冊**。

3. 找到使用代表流的應用程式並打開它。

4. 點選**API 權限**,然後**新增權限**。

5. 搜索名為 Azure**資料資源管理員**的應用程式並選擇它。

6. 選擇**user_impersonation /存取庫托**。

7. 按下「**添加權限**」。

**第二步:在伺服器代碼執行權杖交換**

```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Exchange your token for for Kusto token.
// You will need to provide your application's client ID and secret to authenticate your application 
var tokenForKusto = authContext.AcquireTokenAsync(
    "https://{serviceNameAndRegion}.kusto.windows.net",
    new ClientCredential(customerAadWebApplicationClientId, customerAAdWebApplicationSecret),
    new UserAssertion(customerAadWebApplicationToken)).GetAwaiter().GetResult();
```

**第三步:向 Kusto 用戶端函式庫提供權杖並執行查詢**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}", 
    clusterName, 
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>Web 用戶端 (JAvaScript) 身份驗證與授權



**AAD 應用程式設定**

> [!NOTE]
> 除了設置 AAD 應用需要遵循的標準[步驟](./how-to-provision-aad-app.md)外,還應在 AAD 應用程式中啟用 oauth 隱式流。 您可以通過在 azure 門戶中從應用程式頁中選擇清單來實現這一點,並將 oauth2Allow 隱式Flow設置為 true。

**詳細資料**

當用戶端是在用戶的瀏覽器中運行的 JavaScript 代碼時,將使用隱式授予流。 授予用戶端應用程式對 Azure 資料資源管理員服務的訪問許可權的權杖在成功身份驗證後立即提供,作為重定向 URI 的一部分(在 URI 片段中);此流中未提供刷新令牌,因此客戶端無法長時間緩存權杖並重用它。

與本機用戶端流一樣,應該有兩個 AAD 應用程式(伺服器和用戶端)具有配置的關係。 

AdalJs 要求在撥打任何access_token呼叫之前獲得id_token。

訪問權杖是通過調用`AuthenticationContext.login()`方法獲得的,並且通過調`Authenticationcontext.acquireToken()`用 獲取access_tokens。

* 使用正確的設定建立身份驗證內容:

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* 如果您`authContext.login()`未登`acquireToken()`入, 請先呼叫。 不知道您是否登入的好方法是`authContext.getCachedUser()`打電話 檢視是否`false`傳回)
* 每當`authContext.handleWindowCallback()`頁面載入時,都會打電話。 這是攔截重定向從 AAD 返回並從片段 URL 中拉出權杖並緩存它的程式碼段。
* 呼叫`authContext.acquireToken()`以取得實際存取權杖,現在您已具有有效的 ID 權杖。 獲取Token的第一個參數是 Kusto 伺服器 AAD 應用程式資源 URL。  

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* 在回調中,可以在 Azure 數據資源管理員請求中將權杖用作承載權杖。 例如：

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

> 警告 ─ 如果您在認證時發生以下或類似異常:`ReferenceError: AuthenticationContext is not defined` 
這可能是因為全域命名空間中沒有身份驗證上下文。 遺憾的是,AdalJS 當前有一個未記錄的要求,即身份驗證上下文將在全域命名空間中定義。