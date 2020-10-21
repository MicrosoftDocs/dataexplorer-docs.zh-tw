---
title: 查詢/管理 HTTP 要求-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢/管理 HTTP 要求。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: cf9f9e5f6a9c5afca58e2637ed4e639882e3749d
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337409"
---
# <a name="querymanagement-http-request"></a>查詢/管理 HTTP 要求

## <a name="request-verb-and-resource"></a>要求動詞和資源

|動作    |HTTP 指令動詞|HTTP 資源   |
|----------|---------|----------------|
|查詢     |GET      |`/v1/rest/query`|
|查詢     |POST     |`/v1/rest/query`|
|查詢 v2  |GET      |`/v2/rest/query`|
|查詢 v2  |POST     |`/v2/rest/query`|
|管理性|POST     |`/v1/rest/mgmt` |

例如，若要將控制命令 ( "management" ) 傳送至服務端點，請使用下列要求行：

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

請參閱以下內容，以瞭解要包含的要求標頭和主體。

## <a name="request-headers"></a>要求標頭

下表包含用於查詢和管理作業的一般標頭。

|標準標頭  |說明                                                                                 |必要/選用 |
|-----------------|--------------------------------------------------------------------------------------------|------------------|
|`Accept`         |設定為 `application/json`                                                                   |必要          |
|`Accept-Encoding`|支援的編碼為 `gzip` 和 `deflate`                                                |選擇性          |
|`Authorization`  |請參閱 [驗證](./authentication.md)                                                   |必要          |
|`Connection`     |建議您啟用 `Keep-Alive`                                                   |選擇性          |
|`Content-Length` |建議您在已知時指定要求本文長度                            |選擇性          |
|`Content-Type`   |設定為 `application/json` with `charset=utf-8`                                              |必要          |
|`Expect`         |可以設定為 `100-Continue`                                                                |選擇性          |
|`Host`           |設定為要求傳送至 (的限定功能變數名稱，例如， `help.kusto.windows.net`)  |必要|

下表包含用於查詢和管理作業的一般自訂標頭。 除非另有指示，否則這些標頭只適用于遙測用途，且不會影響任何功能。

所有標頭都是選擇性的。 建議您指定 `x-ms-client-request-id` 自訂標頭。 在某些情況下（例如，取消執行中的查詢），需要此標頭，因為它是用來識別要求。

|自訂標頭           |說明                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |提出要求之應用程式的 (易記) 名稱                                                 |
|`x-ms-user`             |提出要求之使用者的 (易記) 名稱                                                        |
|`x-ms-user-id`          |與 `x-ms-user` 相同                                                                                       |
|`x-ms-client-request-id`|要求的唯一識別碼                                                                       |
|`x-ms-client-version`   |提出要求之用戶端的 (易記) 版本識別碼                                       |
|`x-ms-readonly`         |如果有指定，會強制要求在唯讀模式中執行，使其無法進行長期持續變更 |

## <a name="request-parameters"></a>要求參數

您可以在要求中傳遞下列參數。 這些參數在要求中是以查詢參數或主體的一部分進行編碼，視是否使用 GET 或 POST 而定。

|參數   |說明                                                                                 |必要/選用 |
|------------|--------------------------------------------------------------------------------------------|------------------|
|`csl`       |要執行之查詢或控制命令的文字                                             |必要          |
|`db`        |查詢或控制命令的目標範圍中的資料庫名稱            |某些控制命令是選擇性的。 <br>其他命令和所有查詢都需要。 </br>                                                                   |
|`properties`|提供用戶端要求屬性，以修改要求的處理方式及其結果。 如需詳細資訊，請參閱 [用戶端要求屬性](../netfx/request-properties.md)                                               | 選擇性         |

## <a name="get-query-parameters"></a>取得查詢參數

使用 GET 時，要求的查詢參數會指定要求參數。

## <a name="body"></a>body

使用 POST 時，要求的主體是以 UTF-8 編碼的單一 JSON 檔，其中包含要求參數的值。

## <a name="examples"></a>範例

此範例顯示查詢的 HTTP POST 要求。

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

要求標頭

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

Request body

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

此範例示範如何使用 [捲曲](https://curl.haxx.se/)來建立傳送上述查詢的要求。

1. 取得權杖以進行驗證。

    `AAD_TENANT_NAME_OR_ID` `AAD_APPLICATION_ID` `AAD_APPLICATION_KEY` 在設定[AAD 應用程式驗證](../../../provision-azure-ad-app.md)之後，以相關的值取代、和。

    ```
    curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
      -F "grant_type=client_credentials" \
      -F "resource=https://help.kusto.windows.net" \
      -F "client_id=AAD_APPLICATION_ID" \
      -F "client_secret=AAD_APPLICATION_KEY"
    ```

    此程式碼片段會提供您持有人權杖。

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

1. 在查詢端點的要求中使用持有人權杖。

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

1. 根據 [此規格](response.md)讀取回應。