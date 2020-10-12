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
ms.openlocfilehash: 2642ffc6b87afab785dc5f7ba962e1f659232cc2
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942262"
---
# <a name="querymanagement-http-response"></a>查詢/管理 HTTP 回憶

## <a name="response-status"></a>回應狀態

HTTP 回應狀態行會遵循 HTTP 標準回應碼。
例如，程式碼200表示成功。 

下列狀態碼目前正在使用中，但可能會傳回任何有效的 HTTP 程式碼。

|程式碼|子        |描述                                    |
|----|---------------|-----------------------------------------------|
|100 |繼續       |用戶端可以繼續傳送要求。       |
|200 |[確定]             |要求已成功開始處理。       |
|400 |BadRequest     |要求的格式不正確，且 (永久) 失敗。|
|401 |未經授權   |用戶端必須先進行驗證。            |
|403 |禁止      |用戶端要求遭到拒絕。                      |
|404 |NotFound       |要求參考不存在的實體。      |
|413 |PayloadTooLarge|要求承載超過限制。               |
|429 |TooManyRequests|因為節流，所以已拒絕要求。 |
|504 |逾時        |要求已超時。                         |
|520 |ServiceError   |服務在處理要求時發現錯誤。|

> [!NOTE]
> 200狀態碼會顯示要求處理已順利啟動，而不是已順利完成。
> 在200狀態碼傳回之後，要求處理期間發生的失敗稱為「部分查詢失敗」，當遇到這些錯誤時，會在回應資料流程中插入特殊指標，以警告用戶端所發生的情況。

## <a name="response-headers"></a>回應標頭

將會傳回下列自訂標頭。

|自訂標頭           |描述                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-client-request-id`|在相同名稱的要求標頭中傳送的唯一要求識別碼，或某些唯一的識別碼。     |
|`x-ms-activity-id`      |要求的全域唯一相互關聯識別碼。 它是由服務所建立。                    |

## <a name="response-body"></a>回應本文

如果狀態碼為200，則回應主體是 JSON 檔，會將查詢或控制命令的結果編碼為矩形資料表序列。
如需詳細資料，請參閱下文。

> [!NOTE]
> 資料表的順序是由 SDK 所反映。 例如，使用 .NET Framework Kusto 程式庫時，資料表的順序接著會成為 SDK 所傳回之物件中的結果 `System.Data.IDataReader` 。

如果狀態碼指出4xx 或5xx 錯誤（非401），則回應主體是 JSON 檔，會將失敗的詳細資料編碼。
如需詳細資訊，請參閱 [Microsoft REST API 的指導方針](https://github.com/microsoft/api-guidelines)。

> [!NOTE]
> 如果 `Accept` 要求中未包含標頭，失敗的回應主體不一定是 JSON 檔。

## <a name="json-encoding-of-a-sequence-of-tables"></a>一連串資料表的 JSON 編碼

資料表序列的 JSON 編碼是具有下列名稱/值配對的單一 JSON 屬性包。

|名稱  |值                              |
|------|-----------------------------------|
|資料表|資料表屬性包的陣列。|

資料表屬性包具有下列名稱/值配對。

|名稱     |值                               |
|---------|------------------------------------|
|TableName|識別資料表的字串。 |
|資料行  |資料行屬性包的陣列。|
|資料列     |資料列陣列的陣列。          |

資料行屬性包具有下列名稱/值配對。

|名稱      |值                                                          |
|----------|---------------------------------------------------------------|
|ColumnName|識別此資料行的字串。                           |
|DataType  |提供資料行之近似 .NET 型別的字串。|
|ColumnType|提供資料行純量 [資料類型](../../query/scalar-data-types/index.md) 的字串。|

資料列陣列的順序與個別的資料行陣列相同。
資料列陣列也有一個元素與相關資料行的資料列值一致。
無法以 JSON 表示的純量資料類型（例如 `datetime` 和 `timespan` ）會以 json 字串表示。

下列範例會顯示一個可能的物件，其中包含名為的單一資料表 `Table_0` ，而該資料表具有 `Text` 類型的單一資料 `string` 行和單一資料列。

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

:::image type="content" source="../images/rest-json-representation.png" alt-text="螢幕擷取畫面，顯示 JSON 檔案的樹狀檢視，其中包含資料表物件的陣列。":::

## <a name="the-meaning-of-tables-in-the-response"></a>回應中資料表的意義

在大部分的情況下，控制命令會傳回具有單一資料表的結果，其中包含 control 命令所產生的資訊。 例如，此命令會傳回 `.show databases` 單一資料表，其中包含叢集中所有可存取資料庫的詳細資料。

查詢通常會傳回多個資料表。
針對每個 [表格式運算式語句](../../query/tabularexpressionstatements.md)，會依序產生一或多個資料表，表示語句所產生的結果。

> [!NOTE]
> 可能有多個這類資料表，因為 [批次](../../query/batches.md) 和 [分叉運算子](../../query/forkoperator.md)) 。

通常會產生三個數據表：
* @ExtendedProperties提供其他值的資料表，例如用戶端視覺效果的指示。 例如，這些值會產生，以反映轉譯 [運算子](../../query/renderoperator.md)) 和 [資料庫資料指標](../../management/databasecursor.md)中的資訊。
  
  此資料表具有類型的單一資料行 `string` ，並保留類似 JSON 的值：

  |值|
  |-----|
  |{"視覺效果"： "圓形圖",...}|
  |{"Cursor"： "637239957206013576"}|

* QueryStatus 資料表，提供查詢本身執行的其他相關資訊，例如，如果作業成功完成，以及查詢所耗用的資源為何。

  此資料表具有下列結構：

  |時間戳記                  |Severity|SeverityName|StatusCode|StatusDescription            |計數|RequestId|ActivityId|SubActivityId|ClientActivityId|
  |---------------------------|--------|------------|----------|-----------------------------|-----|---------|----------|-------------|----------------|
  |2020-05-02 06：09：12.7052077|4       |Info        | 0        | 已成功完成查詢|1    |...      |...       |...          |...             |

  2或更小的嚴重性值表示失敗。

* 最後建立的 TableOfContents 資料表，並列出結果中的其他資料表。 

  此資料表的範例如下：

  |序數|種類            |名稱               |Id                                  |PrettyName|
  |-------|----------------|-------------------|------------------------------------|----------|
  |0      | QueryResult    |PrimaryResult      |db9520f9-0455-4cb5-b257-53068497605a||
  |1      | QueryProperties|@ExtendedProperties|908901f6-5319-4809-ae9e-009068c267c7||
  |2      | QueryStatus    |QueryStatus        |00000000-0000-0000-0000-000000000000||
