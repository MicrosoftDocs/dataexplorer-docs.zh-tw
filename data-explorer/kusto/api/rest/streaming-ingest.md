---
title: 串流式引入 HTTP 請求 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的流式引入 HTTP 請求。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d14987806bbc62dbc79112700bd5b88aaa6676c3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503004"
---
# <a name="streaming-ingestion-http-request"></a>串流式引入 HTTP 要求

## <a name="request-verb-and-resource"></a>要求謂詞和資源

|動作    |HTTP 指令動詞|HTTP 資源                                               |
|----------|---------|------------------------------------------------------------|
|擷取    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>要求參數

| 參數    |  描述                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
| `{database}` | **必要**引入要求的目標資料庫的名稱                                          |
| `{table}`    | **必要**引入要求的目標表的名稱                                             |

## <a name="additional-parameters"></a>其他參數
其他參數設定為網址查詢`{name}`=`{value}`: 由&字元壓縮的對


| 參數    |  描述                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
|`streamFormat`| **必要**指定請求正文中資料的格式。 值應為以下值之一: `Csv` `Tsv``Scsv``SOHsv`、`Psv``Json``SingleJson``MultiJson`、`Avro`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 有關詳細資訊,請參閱[支援的資料格式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)。|
|`mappingName` | 如果`streamFormat``Json`為`SingleJson``MultiJson`,`Avro`或 「可選」之一,**則為****「可選**」 。 該值應是表上定義的預先創建的引入映射的名稱。 關於資料映射的詳細資訊,請參考[資料映射](../../management/mappings.md)。 [此處](../../management/create-ingestion-mapping-command.md)介紹了管理表上預先建立的映射的方法 |
              

例如,將 CSV 格式的資料`Logs``Test`引入資料庫中的表中,請使用以下請求行:

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

以預先建立的映射引入 JSON 格式的資料`mylogmapping`

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```


(有關要包括的請求標頭和正文,請參閱下文。

## <a name="request-headers"></a>要求標頭

下表包含用於執行查詢和管理操作的通用標頭。

|標準標頭  |描述                                                                                                              |
|------------------|------------------------------------------------------------------------------------------------------------------------|
|`Accept`          |**選擇項**。 將此選項設定為 `application/json`。                                                                           |
|`Accept-Encoding` |**選擇項**。 支援的編碼是`gzip`與`deflate`。                                                             |
|`Authorization`   |**必要項**。 請參考[身份認證](./authentication.md)。                                                                |
|`Connection`      |**選擇項**。 建議`Keep-Alive`啟用。                                                           |
|`Content-Length`  |**選擇項**。 建議在已知時指定請求正文長度。                                   |
|`Content-Encoding`|**選擇項**。 可以設定為`gzip`在這種情況下需要 gzip 壓縮                                 |
|`Expect`          |**選擇項**。 可以設定為`100-Continue`。                                                                             |
|`Host`            |**必要項**。 將此設定為請求發送到的完全限定的功能變數名稱(例如`help.kusto.windows.net`。|

下表包含執行查詢和管理操作時使用的常見自定義標頭。 除非另有說明,否則這些標頭僅用於遙測目的,並且不會影響功能。

所有標頭都是**選擇的**。 但是 **,** 強烈建議`x-ms-client-request-id`指定 自訂標頭。 在某些情況下(例如,取消正在運行的查詢),此**標頭是必需的,** 因為它用於標識請求。


|自訂標頭           |描述                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |發出請求的應用程式的(友好)名稱。                                                |
|`x-ms-user`             |發出請求的使用者的(友好)名稱。                                                       |
|`x-ms-user-id`          |與 `x-ms-user` 相同。                                                                                      |
|`x-ms-client-request-id`|要求的唯一識別碼。                                                                      |
|`x-ms-client-version`   |發出請求的用戶端的(友好)版本識別碼。                                      |

## <a name="body"></a>body

正文是要攝用的實際數據。 需要使用 UTF-8 編碼的文字格式。

## <a name="examples"></a>範例

下面的範例顯示引入 JSON 內容的 HTTP POST 請求:

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

下面的範例顯示用於引入壓縮的相同資料的 HTTP POST 請求

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