---
title: Kusto 連接字串-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Kusto 連接字串。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 8c5ade644f383a5a0d9e846b1a3143027d1eb467
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863178"
---
# <a name="kusto-connection-strings"></a>Kusto 連接字串

Kusto 連接字串可以提供 Kusto 用戶端應用程式建立 Kusto 服務端點連接所需的資訊。 Kusto 連接字串會在 ADO.NET 連接字串之後進行模型化。 也就是說，連接字串是以分號分隔的名稱/值參數組清單，並選擇性地加上單一 URI。

**範例：**

```text
https://help.kusto.windows.net/Samples; Fed=true; Accept=true
```

URI 會提供要與通訊的服務端點：

* （ `https://help.kusto.windows.net` ）-屬性的值 `Data Source` 。
* `Samples`（預設資料庫）-屬性的值 `Initial Catalog` 。

使用名稱/值語法提供兩個額外的屬性： 

* `Fed`屬性（也稱為 `AAD Federated Security` ）會設定為 `true` 。
* `Accept`屬性設定為 `true` 。

> [!NOTE]
>
> * 屬性名稱不區分大小寫，且名稱/值組之間的空格會被忽略。
> * 屬性值**會**區分大小寫。 包含分號（ `;` ）、單引號（ `'` ）或雙引號（）的屬性值， `"` 必須以雙引號括住。

有數個 Kusto 用戶端工具支援連接字串之 URI 前置詞的延伸模組，因為它們允許使用速記格式 `@` _ClusterName_ `/` _InitialCatalog_ 。
例如， `@help/Samples` 這些工具會將連接字串轉譯為 `https://help.kusto.windows.net/Samples; Fed=true` ，這表示三個屬性（ `Data Source` 、 `Initial Catalog` 和 `AAD Federated Security` ）。

以程式設計方式，可由 c # 類別剖析和操作 Kusto 連接字串 `Kusto.Data.KustoConnectionStringBuilder` 。 這個類別會驗證所有連接字串，並在驗證失敗時產生執行時間例外狀況。
這項功能存在於所有種類的 Kusto SDK 中。

## <a name="connection-string-properties"></a>連接字串屬性

下表列出您可以在 Kusto 連接字串中指定的所有屬性。
它會列出程式設計名稱（這是物件中的屬性名稱 `Kusto.Data.KustoConnectionStringBuilder` ），以及其他別名的屬性名稱。

### <a name="general-properties"></a>一般屬性

| 屬性名稱              | 別名                      | 程式設計名稱  | 描述                                                                                                                          |
|----------------------------|----------------------------------------|--------------------|---------------------------------------------------|
| 用於追蹤的用戶端版本 |                                        | TraceClientVersion | 當追蹤用戶端版本時，請使用此值   |
| 資料來源                | 位址、位址、網路位址、伺服器 | DataSource         | 指定 Kusto 服務端點的 URI。 例如，`https://mycluster.kusto.windows.net` 或 `net.tcp://localhost`。               |
| 初始目錄            | 資料庫                               | InitialCatalog     | 預設所要使用之資料庫的名稱。 例如，MyDatabase|
| 查詢一致性          | QueryConsistency                       | QueryConsistency   | 設定為 `strongconsistency` 或 `weakconsistency` ，以判斷查詢是否應該在執行之前與元資料同步處理 |

### <a name="user-authentication-properties"></a>使用者驗證屬性

| 屬性名稱          | 別名                          | 程式設計名稱 | 描述                       |
|------------------------|--------------------------------------------|-------------------|-----------------------------------|
| AAD 同盟安全性 | 聯合安全性、同盟、饋送、AADFed | FederatedSecurity | 指示用戶端執行 Azure Active 的布林值  |
| 強制執行 MFA            | MFA、EnforceMFA                             | EnforceMfa        | 布林值，指示用戶端取得多重要素驗證 token       |
| 使用者識別碼                | UID，使用者                                  | UserID            | 字串值，指示用戶端以指定的使用者名稱執行使用者驗證           |
| 用於追蹤的使用者名稱  |                                            | TraceUserName     | 一個字串值，會向服務報告在內部追蹤要求時所要使用的使用者名稱         |
| 使用者 Token             | UsrToken、UserToken                        | UserToken         | 字串值，指示用戶端使用指定的持有人權杖來執行使用者驗證。<br/>覆寫 ApplicationClientId、ApplicationKey 和 ApplicationToken。 （如果指定，則會略過實際的用戶端驗證流程，而改用提供的權杖）。       |
| 命名空間              | NS                                         | 命名空間         | （供日後使用）  |



### <a name="application-authentication-properties"></a>應用程式驗證屬性

|屬性名稱                                     |別名                         |程式設計名稱                             |描述      |
|--------------------------------------------------|------------------------------------------|----------------------------------------------|-----------------|
|AAD 同盟安全性                            |聯合安全性、同盟、饋送、AADFed|FederatedSecurity                             |布林值，指示用戶端執行 Azure Active Directory （AAD）同盟驗證|
|應用程式憑證指紋                |Appcert.exe                                   |ApplicationCertificateThumbprint              |字串值，提供使用應用程式用戶端憑證驗證流程時所要使用之用戶端憑證的指紋|
|應用程式用戶端識別碼                             |AppClientId                               |ApplicationClientId                           |字串值，提供驗證時要使用的應用程式用戶端識別碼|
|應用程式金鑰                                   |AppKey                                    |ApplicationKey                                |字串值，提供使用應用程式秘密流程進行驗證時，所要使用的應用程式金鑰|
|追蹤的應用程式名稱                      |TraceAppName                              |ApplicationNameForTracing                     |一個字串值，會向服務報告在內部追蹤要求時所要使用的應用程式名稱|
|應用程式權杖                                 |AppToken                                  |ApplicationToken                              |字串值，指示用戶端使用指定的持有人權杖來執行應用程式驗證|
|授權單位識別碼                                      |TenantId                                  |授權單位                                     |字串值，提供註冊應用程式的租使用者名稱或識別碼|
|                                                  |                                          |EmbeddedManagedIdentity                       |字串值，指示用戶端要搭配受控識別驗證使用哪個應用程式識別;使用 `system` 來表示系統指派的身分識別。 這個屬性不能使用連接字串來設定，只能以程式設計方式進行。|ManagedServiceIdentity                        |TODO|
|應用程式憑證主體辨別名稱|應用程式憑證主體           |ApplicationCertificateSubjectDistinguishedName||
|應用程式憑證簽發者辨別名稱 |應用程式憑證簽發者            |ApplicationCertificateIssuerDistinguishedName ||
|應用程式憑證傳送公開憑證   |應用程式憑證 SendX5c、SendX5c  |ApplicationCertificateSendPublicCertificate   ||



### <a name="client-communication-properties"></a>用戶端通訊屬性

|屬性名稱                      |別名|程式設計名稱  |描述                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|接受      ||接受      |布林值，要求失敗時傳回的詳細錯誤物件。|
|串流   ||串流   |要求用戶端的布林值不會在提供給呼叫者之前累積資料。|
|未壓縮||未壓縮|要求用戶端不會要求傳輸層級壓縮的布林值。|

## <a name="authentication-properties-details"></a>驗證屬性（詳細資料）

連接字串的其中一項重要工作，是告訴用戶端如何向服務驗證。
用戶端通常會使用下列演算法來對 HTTP/HTTPS 端點進行驗證：

1. 如果 AadFederatedSecurity 為 true：
    1. 如果指定 UserToken，請使用 AAD 同盟驗證搭配指定的權杖
    1. 否則，如果指定 ApplicationToken，請使用指定的權杖執行同盟驗證
    1. 否則，如果指定 ApplicationClientId 和 ApplicationKey，請使用指定的應用程式用戶端識別碼和金鑰來執行同盟驗證
    1. 否則，如果指定 ApplicationClientId 和 ApplicationCertificateThumbprint，請使用指定的應用程式用戶端識別碼和憑證來執行同盟驗證
    1. 否則，請使用目前登入的使用者身分識別執行同盟驗證（如果這是會話中的第一次驗證，則會提示使用者）

1. 否則不會驗證。


### <a name="aad-federated-application-authentication-with-application-certificate"></a>使用應用程式憑證的 AAD 同盟應用程式驗證

1. 只有 web 應用程式支援以應用程式憑證為基礎的驗證（而不是原生用戶端應用程式）。
1. Web 應用程式應設定為接受指定的憑證。 [如何根據 AAD 應用程式的憑證進行驗證](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
1. Web 應用程式應設定為相關 Kusto 叢集中的授權主體。
1. 應安裝具有指定之指紋的憑證（在 [本機電腦] 存放區或在 [目前使用者存放區] 中）。
1. 憑證的公開金鑰至少應包含2048位。

## <a name="aad-based-authentication-examples"></a>以 AAD 為基礎的驗證範例

**使用目前登入的使用者身分識別進行 AAD 同盟驗證（如有需要，系統會提示使用者）**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;Authority Id={authority}"
```

**具有使用者識別碼提示的 AAD 同盟驗證（如有需要，系統會提示使用者）**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var userUPN = "johndoe@contoso.com";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);
kustoConnectionStringBuilder.UserID = userUPN;

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    UserID = userUPN,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;User ID={userUPN};Authority Id={authority}"
```

**使用 ApplicationClientId 和 ApplicationKey 的 AAD 同盟應用程式驗證**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = <ApplicationClientId>;
var applicationKey = <ApplicationKey>;

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationKeyAuthentication(applicationClientId, applicationKey, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    ApplicationClientId = applicationClientId,
    ApplicationKey = applicationKey,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppKey={applicationKey};Authority Id={authority}"
```

**使用使用者/應用程式權杖的 AAD 同盟驗證**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var access_token = "<access token obtained from AAD>"

// Recommended syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadUserTokenAuthentication(access_token, authority);

// Legacy syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    UserToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: "Data Source={serviceUri};Database=NetDefaultDB;Fed=True;UserToken={access_token};Authority Id={authority}"

// Recommended syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationTokenAuthentication(access_token, authority);

// Legacy syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppToken={applicationToken};Authority Id={authority}"
```

**使用權杖提供者回呼（每次需要權杖時都會叫用）**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
Func<string> tokenProviderCallback; // User-defined method to retrieve the access token

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadTokenProviderAuthentication(tokenProviderCallback);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    TokenProviderCallback = () => Task.FromResult(tokenProviderCallback()),
};
```

**使用受控識別**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var managedIdentity = "<managed identity>"; // For system-assigned identity use "system"

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadManagedIdentity(managedIdentity);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    EmbeddedManagedIdentity = managedIdentity,
};
```

**使用 x.509 憑證**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
X509Certificate2 applicationCertificate = "<certificate blob>";
bool sendX5c = <desired value>; // Set too 'True' to use Trusted Issuer feature of AAD

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationCertificateAuthentication(applicationClientId, applicationCertificate, authority, sendX5c);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateBlob = applicationCertificate,
    ApplicationCertificateSendX5c = sendX5c,
    Authority = authority,
};
```

**依指紋使用 x.509 憑證（用戶端會嘗試從本機存放區載入憑證）**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
string applicationCertificateThumbprint = "<ApplicationCertificateThumbprint>";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationThumbprintAuthentication(applicationClientId, applicationCertificateThumbprint, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateThumbprint = applicationCertificateThumbprint,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppCert={applicationCertificateThumbprint};Authority Id={authority}"
```
