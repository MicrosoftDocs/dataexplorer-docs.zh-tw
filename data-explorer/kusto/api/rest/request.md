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
ms.openlocfilehash: 2c6efc03ea252eba5ed63e99d9214e59113856e9
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373574"
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

例如，若要將控制命令（「管理」）傳送至服務端點，請使用下列要求行：

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

請參閱下方，以取得要包含的要求標頭和主體。

## <a name="request-headers"></a>要求標頭

下表包含用於查詢和管理作業的一般標頭。

|標準標頭  |描述                                                                                 |必要/選用 |
|-----------------|--------------------------------------------------------------------------------------------|------------------|
|`Accept`         |設定為 `application/json`                                                                   |必要          |
|`Accept-Encoding`|支援的編碼方式為 `gzip` 和`deflate`                                                |選用          |
|`Authorization`  |請參閱[驗證](./authentication.md)                                                   |必要          |
|`Connection`     |我們建議您啟用`Keep-Alive`                                                   |選用          |
|`Content-Length` |我們建議您在已知的情況時指定要求主體長度                            |選用          |
|`Content-Type`   |將設定為 `application/json` with`charset=utf-8`                                              |必要          |
|`Expect`         |可以設定為`100-Continue`                                                                |選用          |
|`Host`           |將設定為傳送要求的合格功能變數名稱（例如， `help.kusto.windows.net` ） |必要|

下表包含用於查詢和管理作業的一般自訂標頭。 除非另有指示，否則這些標頭僅用於遙測用途，而且不會影響任何功能。

所有標頭都是選擇性的。 我們建議您指定 `x-ms-client-request-id` 自訂標頭。 在某些情況下，例如取消執行中的查詢，則需要此標頭，因為它是用來識別要求。

|自訂標頭           |描述                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |提出要求之應用程式的（易記）名稱                                                 |
|`x-ms-user`             |提出要求之使用者的（易記）名稱                                                        |
|`x-ms-user-id`          |與 `x-ms-user` 相同                                                                                       |
|`x-ms-client-request-id`|要求的唯一識別碼                                                                       |
|`x-ms-client-version`   |提出要求之用戶端的（易記）版本識別碼                                       |
|`x-ms-readonly`         |如果指定，會強制要求在唯讀模式下執行，以防止它進行長期的變更 |

## <a name="request-parameters"></a>要求參數

您可以在要求中傳遞下列參數。 它們會在要求中編碼為查詢參數，或作為本文的一部分，端視是否使用 GET 或 POST 而定。

|參數   |描述                                                                                 |必要/選用 |
|------------|--------------------------------------------------------------------------------------------|------------------|
|`csl`       |要執行之查詢或控制命令的文字                                             |必要          |
|`db`        |範圍中的資料庫名稱，這是查詢或控制命令的目標            |針對某些控制命令則為選擇性。 <br>其他命令和所有查詢都需要此參數。 </br>                                                                   |
|`properties`|提供用戶端要求屬性，可修改處理要求的方式及其結果。 如需詳細資訊，請參閱[用戶端要求屬性](../netfx/request-properties.md)                                               | 選用         |

## <a name="get-query-parameters"></a>取得查詢參數

當使用 GET 時，要求的查詢參數會指定要求參數。

## <a name="body"></a>body

使用 POST 時，要求的主體是以 UTF-8 編碼的單一 JSON 檔，其中包含要求參數的值。

## <a name="examples"></a>範例

這個範例會顯示查詢的 HTTP POST 要求。

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

這個範例示範如何使用[捲曲](https://curl.haxx.se/)來建立傳送上述查詢的要求。

1. 取得驗證的權杖。

    `AAD_TENANT_NAME_OR_ID` `AAD_APPLICATION_ID` `AAD_APPLICATION_KEY` 設定[AAD 應用程式驗證](../../management/access-control/how-to-provision-aad-app.md)之後，請將、和取代為相關的值

    ```
    curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
      -F "grant_type=client_credentials" \
      -F "resource=https://help.kusto.windows.net" \
      -F "client_id=AAD_APPLICATION_ID" \
      -F "client_secret=AAD_APPLICATION_KEY"
    ```

    此程式碼片段會為您提供持有人權杖。

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

1. 根據[此規格](response.md)來讀取回應。
