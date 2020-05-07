---
title: 串流內嵌 HTTP 要求-Azure 資料總管
description: 本文說明 Azure 資料總管中的串流內嵌 HTTP 要求。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1614a04c5e5bff51f45df914174c967ff9c7d8a2
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907084"
---
# <a name="streaming-ingestion-http-request"></a>串流內嵌 HTTP 要求

## <a name="request-verb-and-resource"></a>要求動詞和資源

|動作    |HTTP 指令動詞|HTTP 資源                                               |
|----------|---------|------------------------------------------------------------|
|擷取    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>要求參數

| 參數    | 描述                                                                 | 必要/選用 |
|--------------|-----------------------------------------------------------------------------|-------------------|
| `{database}` |   內嵌要求的目標資料庫名稱                     |  必要         |
| `{table}`    |   內嵌要求的目標資料表名稱                        |  必要         |

## <a name="additional-parameters"></a>其他參數

其他參數會格式化為 URL 查詢`{name}={value}`配對，並以 & 字元分隔。

| 參數    | 描述                                                                          | 必要/選用   |
|--------------|--------------------------------------------------------------------------------------|---------------------|
|`streamFormat`| 指定要求主體中的資料格式。 這個值應該是下列其中一個`CSV`： `TSV`、 `SCsv`、 `SOHsv`、 `PSV`、 `JSON`、 `MultiJSON`、 `Avro`、。 如需詳細資訊，請參閱[支援的資料格式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)。| 必要 |
|`mappingName` | 在資料表上定義之預先建立的內嵌對應名稱。 如需詳細資訊，請參閱[資料](../../management/mappings.md)對應。 [這裡](../../management/create-ingestion-mapping-command.md)說明了在資料表上管理預先建立之對應的方式。| 選擇性，但如果`streamFormat`是`JSON`、 `MultiJSON`或其中一個，則為必要項`Avro`|  |
              
例如，若要將 CSV 格式的資料內嵌到`Logs`資料庫`Test`中的資料表，請使用：

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

若要使用預先建立的對應`mylogmapping`來內嵌 JSON 格式的資料，請使用：

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

## <a name="request-headers"></a>要求標頭

下表包含查詢和管理作業的一般標頭。

|標準標頭   | 描述                                                                               | 必要/選用 | 
|------------------|-------------------------------------------------------------------------------------------|-------------------|
|`Accept`          | 將此值設定`application/json`為。                                                     | 選用          |
|`Accept-Encoding` | 支援的編碼`gzip`方式`deflate`為和。                                             | 選用          | 
|`Authorization`   | 請參閱[驗證](./authentication.md)。                                                | 必要          |
|`Connection`      | 啟用 `Keep-Alive`。                                                                      | 選用          |
|`Content-Length`  | 指定要求主體長度（如果已知）。                                              | 選用          |
|`Content-Encoding`| 設定為`gzip` ，但主體必須是 gzip 壓縮                                        | 選用          |
|`Expect`          | 設定為 `100-Continue`。                                                                    | 選用          |
|`Host`            | 將設定為您用來傳送要求的功能變數名稱（例如`help.kusto.windows.net`）。 | 必要          |

下表包含查詢和管理作業的一般自訂標頭。 除非另有指示，否則標頭僅適用于遙測用途，而且不會影響任何功能。

|自訂標頭           |描述                                                                           | 必要/選用 |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |提出要求之應用程式的（易記）名稱。                            | 選用          |
|`x-ms-user`             |提出要求之使用者的（易記）名稱。                                   | 選用          |
|`x-ms-user-id`          |與 `x-ms-user` 相同。                                                                  | 選用          |
|`x-ms-client-request-id`|要求的唯一識別碼。                                                  | 選用          |
|`x-ms-client-version`   |提出要求之用戶端的（易記）版本識別碼。 在案例中為必要項，用來識別要求，例如取消執行中的查詢。                                                        | 選擇性/必要  |

## <a name="body"></a>body

主體是要內嵌的實際資料。 文字格式應使用 UTF-8 編碼。

## <a name="examples"></a>範例

下列範例顯示內嵌 JSON 內容的 HTTP POST 要求：

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

要求標頭：

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 161
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

要求本文：

```txt
{"Timestamp":"2018-11-14 11:34","Level":"Info","EventText":"Nothing Happened"}
{"Timestamp":"2018-11-14 11:35","Level":"Error","EventText":"Something Happened"}
```

下列範例顯示內嵌相同壓縮資料的 HTTP POST 要求。

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

要求標頭：

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 116
Content-Encoding: gzip
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

要求本文：

```
... binary data ...
```
