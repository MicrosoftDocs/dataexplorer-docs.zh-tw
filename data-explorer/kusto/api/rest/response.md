---
title: 查詢/管理 HTTP 回應-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的查詢/管理 HTTP 回應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/05/2018
ms.openlocfilehash: f926daa248a74b7b61ea4867d3a54f857444823e
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226018"
---
# <a name="querymanagement-http-response"></a>查詢/管理 HTTP 回憶

## <a name="response-status"></a>回應狀態

HTTP 回應狀態行會遵循 HTTP 標準回應碼。
例如，程式碼200表示成功。 

下列狀態碼目前正在使用中，但可能會傳回任何有效的 HTTP 程式碼。

|程式碼|代碼        |說明                                    |
|----|---------------|-----------------------------------------------|
|100 |Continue       |用戶端可以繼續傳送要求。       |
|200 |[確定]             |已成功處理要求。       |
|400 |BadRequest     |要求的格式不正確，且失敗（永久）。|
|401 |未經授權   |用戶端必須先進行驗證。            |
|403 |禁止      |已拒絕用戶端要求。                      |
|404 |NotFound       |要求參考了不存在的實體。      |
|413 |PayloadTooLarge|要求承載超過限制。               |
|429 |TooManyRequests|已拒絕要求，因為節流。 |
|504 |逾時        |要求已超時。                         |
|520 |ServiceError   |服務在處理要求時發現錯誤。|

> [!NOTE]
> 200狀態碼顯示要求處理已成功啟動，而不是已順利完成。
> 在200狀態碼傳回之後，要求處理期間遇到的失敗稱為「部分查詢失敗」，而當遇到這些錯誤時，會將特殊指標插入回應資料流程，以警示用戶端發生錯誤。

## <a name="response-headers"></a>回應標頭

將傳回下列自訂標頭。

|自訂標頭           |說明                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-client-request-id`|在相同名稱的要求標頭中傳送的唯一要求識別碼，或一些唯一的識別碼。     |
|`x-ms-activity-id`      |要求的全域唯一相互關聯識別碼。 這是由服務所建立。                    |

## <a name="response-body"></a>回應本文

如果狀態碼為200，則回應主體是 JSON 檔，它會將查詢或控制命令的結果編碼為一系列矩形資料表。
如需詳細資料，請參閱下文。

> [!NOTE]
> SDK 會反映資料表的順序。 例如，使用 .NET Framework Kusto] 程式庫時，資料表的順序會變成 SDK 所傳回之物件中的結果 `System.Data.IDataReader` 。

如果狀態碼指出4xx 或5xx 錯誤，而不是401，回應主體就是將失敗詳細資料編碼的 JSON 檔。
如需詳細資訊，請參閱[Microsoft REST API 指導方針](https://github.com/microsoft/api-guidelines)。

> [!NOTE]
> 如果 `Accept` 要求中未包含標頭，則失敗的回應主體不一定是 JSON 檔。

## <a name="json-encoding-of-a-sequence-of-tables"></a>一系列資料表的 JSON 編碼

一系列資料表的 JSON 編碼是具有下列名稱/值組的單一 JSON 屬性包。

|名稱  |值                              |
|------|-----------------------------------|
|資料表|資料表屬性包的陣列。|

資料表屬性包具有下列名稱/值組。

|名稱     |值                               |
|---------|------------------------------------|
|TableName|識別資料表的字串。 |
|欄  |資料行屬性包的陣列。|
|資料列     |資料列陣列的陣列。          |

資料行屬性包具有下列名稱/值組。

|名稱      |值                                                          |
|----------|---------------------------------------------------------------|
|ColumnName|識別此資料行的字串。                           |
|DataType  |提供資料行之近似 .NET 類型的字串。|
|ColumnType|字串，提供資料行的純量[資料類型](../../query/scalar-data-types/index.md)。|

資料列陣列的順序與個別的資料行陣列相同。
資料列陣列也有一個元素與相關資料行的資料列值一致。
無法以 JSON 表示的純量資料類型（例如 `datetime` 和 `timespan` ）會以 json 字串表示。

下列範例顯示一個可能的這類物件，其中包含名為的單一資料表 `Table_0` ，其具有 `Text` 類型的單一資料 `string` 行和單一資料列。

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

另一個範例： 

:::image type="content" source="../images/rest-json-representation.png" alt-text="rest-json 標記法":::

## <a name="the-meaning-of-tables-in-the-response"></a>回應中資料表的意義

在大部分的情況下，control 命令會傳回含有單一資料表的結果，其中包含 control 命令所產生的資訊。 例如，命令會傳回 `.show databases` 單一資料表，其中包含叢集中所有可存取資料庫的詳細資料。

查詢通常會傳回多個資料表。
針對每個[表格式運算式語句](../../query/tabularexpressionstatements.md)，會依序產生一或多個資料表，表示語句所產生的結果。

> [!NOTE]
> 因為[批次](../../query/batches.md)和[分叉運算子](../../query/forkoperator.md)，可能會有多個這類資料表。

通常會產生三個數據表：
* @ExtendedProperties提供其他值（例如用戶端視覺效果指示）的資料表。 這些值會產生，例如，用來反映[render 運算子](../../query/renderoperator.md)中的資訊）和[資料庫資料指標](../../management/databasecursor.md)。
  
  此資料表具有類型的單一資料行 `string` ，保留類似 JSON 的值：

  |值|
  |-----|
  |{"視覺效果"： "圓形圖",...}|
  |{"Cursor"： "637239957206013576"}|

* QueryStatus 資料表，提供有關執行查詢本身的其他資訊，例如，如果已順利完成或未成功，以及查詢所使用的資源。

  此資料表具有下列結構：

  |時間戳記                  |Severity|SeverityName|StatusCode|StatusDescription            |計數|RequestId|ActivityId|SubActivityId|ClientActivityId|
  |---------------------------|--------|------------|----------|-----------------------------|-----|---------|----------|-------------|----------------|
  |2020-05-02 06：09：12.7052077|4       |資訊        | 0        | 查詢已順利完成|1    |...      |...       |...          |...             |

  2或更小的嚴重性值表示失敗。

* 最後建立的 TableOfContents 資料表，並列出結果中的其他資料表。 

  此資料表的範例如下：

  |Ordinal|種類            |名稱               |Id                                  |PrettyName|
  |-------|----------------|-------------------|------------------------------------|----------|
  |0      | QueryResult    |PrimaryResult      |db9520f9-0455-4cb5-b257-53068497605a||
  |1      | QueryProperties|@ExtendedProperties|908901f6-5319-4809-ae9e-009068c267c7||
  |2      | QueryStatus    |QueryStatus        |00000000-0000-0000-0000-000000000000||
