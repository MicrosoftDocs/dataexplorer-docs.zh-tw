---
title: 查詢/管理 HTTP 回應 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的查詢/管理 HTTP 回應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/05/2018
ms.openlocfilehash: 13937bbc9d17ed570c40926497ccf9b5c942fe72
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503072"
---
# <a name="querymanagement-http-response"></a>查詢/管理 HTTP 回憶

## <a name="response-status"></a>回應狀態

HTTP 回應狀態行遵循 HTTP 標準回應代碼(例如,200 表示成功)。 目前正在使用以下狀態代碼(但請注意,可能會返回任何有效的 HTTP 代碼):

|程式碼|子代碼       |描述                                    |
|----|---------------|-----------------------------------------------|
|100 |Continue       |用戶端可以繼續發送請求。       |
|200 |[確定]             |請求已成功啟動處理。       |
|400 |BadRequest     |請求格式錯誤且失敗(永久)。|
|401 |未經授權   |用戶端需要首先進行身份驗證。            |
|403 |禁止      |用戶端請求被拒絕。                      |
|404 |NotFound       |請求引用不存在的實體。      |
|413 |有效負載過大|請求有效負載超出限制。               |
|429 |TooManyRequests|請求因限制而被拒絕。     |
|504 |逾時        |請求已超時。                         |
|520 |ServiceError   |服務在處理請求時遇到錯誤。|

> [!NOTE]
> 請務必瞭解,200 狀態代碼表示請求處理已成功啟動,而不是成功完成。 請求處理期間遇到但返回 200 後遇到的故障稱為「部分查詢失敗」,當遇到特殊指標時,將注入回應流,以提醒客戶端它們發生。

## <a name="response-headers"></a>回應標頭

將返回以下自定義標頭。

|自訂標頭           |描述                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-client-request-id`|在同名的請求標頭中發送的唯一請求標識符或某些唯一標識符。     |
|`x-ms-activity-id`      |請求的全域唯一關聯標識符(由服務創建)。                        |

## <a name="response-body"></a>Response body

如果狀態代碼為 200,則回應正文是 JSON 文件,該文檔將查詢或控制命令的結果編碼為矩形表的解套。
如需詳細資料，請參閱下文。

> [!NOTE]
> 此表序列由 SDK 反映。 例如,當使用 .NET Framework Kusto.Data 庫時,表序列將成為`System.Data.IDataReader`SDK 返回的物件中的結果。

如果狀態代碼指示 4xx 或 5xx 錯誤(401 以外的錯誤),則回應正文是 JSON 文件,用於編碼故障的詳細資訊,符合 Microsoft [REST API 指南](https://github.com/microsoft/api-guidelines)。

> [!NOTE]
> 如果請求`Accept`中未包含標頭,則失敗的回應正文不一定是 JSON 文檔。

## <a name="json-encoding-of-a-sequence-of-tables"></a>表序列的 JSON 編碼

一系列表的 JSON 編碼是具有以下名稱/值對的單個 JSON 屬性包:

|名稱  |值                              |
|------|-----------------------------------|
|資料表|表屬性包的陣組。|

表屬性套件具有以下名稱/值對:

|名稱     |值                               |
|---------|------------------------------------|
|TableName|標識表的字串。 |
|資料行  |列屬性包的陣組。|
|資料列     |行陣列的陣列。          |

欄屬性「包具有以下名稱/值對:

|名稱      |值                                                          |
|----------|---------------------------------------------------------------|
|ColumnName|識別此資料行的字串。                           |
|DataType  |提供列的近似 .NET 類型的字串。|
|ColumnType|提供欄[位的標量資料類型](../../query/scalar-data-types/index.md)的字串。|

Row 數位的順序與相應的列陣列相同,並且具有一個元素,該元素具有相關列的行值。
不能在 JSON 中表示的 Scalar 數據類型`datetime
and `(如時間跨度)表示為 JSON 字串。

下面的示例顯示一個可能的此類物件,當它包含一個調用的單個`Table_0`表,該表包含`Text`一 個`string`類型的 列和單個行。

```json
{
    "Tables": [{
        "TableName": "Table_0",
        "Columns": [{
            "ColumnName": "Text",
            "DataType": "String",
            "ColumnType": "string"
        }],
        "Rows": [["Hello, World!"]]
}
```

另一個外泄:

![JSON 回應表示](../images/rest-json-representation.png "休息-json 表示")

## <a name="the-meaning-of-tables-in-the-response"></a>回應中表的含義

在大多數情況下,控制命令返回具有單個表的結果,該表保存由控制命令生成的資訊。 例如,該`.show databases`命令返回一個表,其中包含群集中所有可訪問資料庫的詳細資訊。

另一方面,查詢通常返回多個表。 對於每個[表格表達式語句](../../query/tabularexpressionstatements.md),按順序發出一個或多個表,表示該語句生成的結果(由於[批處理](../../query/batches.md)和[分叉運算符](../../query/forkoperator.md),可以有多個此類表)。

此外,通常還生成三個表:

* 表@ExtendedProperties,提供其他值,如用戶端可視化指令(例如,用於反映[呈現運算符](../../query/renderoperator.md)中的資訊)和[資料庫游標](../../management/databasecursor.md)資訊)。
* 查詢狀態表,提供有關查詢本身執行的其他資訊,例如查詢是否成功完成,以及查詢消耗的資源是什麼。
* 目錄目錄,該表最後發出並列出結果中的其他表。

