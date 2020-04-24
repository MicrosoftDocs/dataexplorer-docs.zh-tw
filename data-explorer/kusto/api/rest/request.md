---
title: 查詢/管理 HTTP 請求 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理員中的查詢/管理 HTTP 請求。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 71fac7122ca51755beaa09ce3867143806e4f2b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502715"
---
# <a name="querymanagement-http-request"></a>查詢/管理 HTTP 要求

## <a name="request-verb-and-resource"></a>要求謂詞和資源

|動作    |HTTP 指令動詞|HTTP 資源   |
|----------|---------|----------------|
|查詢     |GET      |`/v1/rest/query`|
|查詢     |POST     |`/v1/rest/query`|
|查詢 v2  |GET      |`/v2/rest/query`|
|查詢 v2  |POST     |`/v2/rest/query`|
|管理性|POST     |`/v1/rest/mgmt` |

例如,要向服務終結點發送控制命令(「管理」),請使用以下請求行:

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

(有關要包括的請求標頭和正文,請參閱下文。

## <a name="request-headers"></a>要求標頭

下表包含用於執行查詢和管理操作的通用標頭。

|標準標頭  |描述                                                                                                                    |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------|
|`Accept`         |**必要項**。 將此選項設定為 `application/json`。                                                                                  |
|`Accept-Encoding`|**選擇項**。 支援的編碼是`gzip`與`deflate`。                                                                    |
|`Authorization`  |**必要項**。 請參考[身份認證](./authentication.md)。                                                                       |
|`Connection`     |**選擇項**。 建議`Keep-Alive`啟用。                                                                  |
|`Content-Length` |**選擇項**。 建議在已知時指定請求正文長度。                                          |
|`Content-Type`   |**必要項**。 將此設定為`application/json``charset=utf-8`。                                                             |
|`Expect`         |**選擇項**。 可以設定為`100-Continue`。                                                                                    |
|`Host`           |**必要項**。 將此設置為請求發送到的完全限定的功能變數名稱(例如)。 `help.kusto.windows.net`|

下表包含執行查詢和管理操作時使用的常見自定義標頭。 除非另有說明,否則這些標頭僅用於遙測目的,並且不會影響功能。

所有標頭都是**選擇的**。 **強烈建議您**`x-ms-client-request-id`指定 自訂標頭。 在某些情況下(例如,取消正在運行的查詢 **)此標頭**是必需的,因為它用於標識請求。


|自訂標頭           |描述                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |發出請求的應用程式的(友好)名稱。                                                |
|`x-ms-user`             |發出請求的使用者的(友好)名稱。                                                       |
|`x-ms-user-id`          |與 `x-ms-user` 相同。                                                                                      |
|`x-ms-client-request-id`|要求的唯一識別碼。                                                                      |
|`x-ms-client-version`   |發出請求的用戶端的(友好)版本識別碼。                                      |
|`x-ms-readonly`         |如果指定,則強制請求在唯讀模式下運行(阻止它進行持久的更改)。|

## <a name="request-parameters"></a>要求參數

可以在請求中傳遞以下參數。 它們在請求中編碼為查詢參數或正文的一部分,具體取決於是否使用 GET 或 POST。

* `csl`:這是一個**必填參數**。 它保存要執行的查詢或控制項命令的文本。

* `db`:這是某些控制命令的**可選**參數,也是其他控制命令和所有查詢的**必選**參數。 它提供"作用域中的資料庫"的名稱 - 查詢或控制命令的目標資料庫。

* `properties`:這是一個**可選**參數。 它提供各種用戶端請求屬性,用於修改處理請求的方式及其結果。 有關完整說明,請參閱[客戶端請求屬性](../netfx/request-properties.md)。

## <a name="get-query-parameters"></a>GET 查詢參數

使用 GET 時,請求的查詢參數指定上述請求參數。

## <a name="body"></a>body

使用 POST 時,請求正文是在 UTF-8 中編碼的單個 JSON 文檔,該文檔提供上述請求參數的值。

## <a name="examples"></a>範例

下面的範例顯示查詢的 HTTP POST 請求:

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

要求標頭：

```txt
Accept: application/json
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Content-Type: application/json; charset=utf-8
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1
x-ms-user-id: EARTH\davidbg
x-ms-app: MyApp
```

請求正文(為清楚起見而引入的新線;不需要):

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

下面的範例展示如何建立使用[curl](https://curl.haxx.se/)傳送上述查詢的請求:

1. 獲取用於身份驗證的權杖。

* `AAD_TENANT_NAME_OR_ID``AAD_APPLICATION_ID`設定`AAD_APPLICATION_KEY` [AAD 應用程式身份驗證](../../management/access-control/how-to-provision-aad-app.md)後,使用相關值取代和

```
curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
  -F "grant_type=client_credentials" \
  -F "resource=https://help.kusto.windows.net" \
  -F "client_id=AAD_APPLICATION_ID" \
  -F "client_secret=AAD_APPLICATION_KEY"
```

這會為您提供無記名權杖:

```
{
  "token_type": "Bearer",
  "expires_in": "3599",
  "ext_expires_in":"3599", 
  "expires_on":"1578439805",
  "not_before":"1578435905",
  "resource":"https://help.kusto.windows.net",
  "access_token":"eyJ0...uXOQ"
}
```

2. 在請求中使用查詢終結點中的無記名權杖:

```
curl -d '{"db":"Samples","csl":"print Test=\"Hello, World!\"","properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"}}"}"' \
-H "Accept: application/json" \
-H "Authorization: Bearer eyJ0...uXOQ" \
-H "Content-Type: application/json; charset=utf-8" \
-H "Host: help.kusto.windows.net" \
-H "x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1" \
-H "x-ms-user-id: EARTH\davidbg" \
-H "x-ms-app: MyApp" \
-X POST https://help.kusto.windows.net/v2/rest/query
```

3. 根據[此規範](response.md)閱讀回應。