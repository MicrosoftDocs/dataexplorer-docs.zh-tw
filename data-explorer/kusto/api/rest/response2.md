---
title: 查詢 V2 HTTP 回應-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的查詢 V2 HTTP 回應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 86a56d77005b2c6b5c9d38bbec85eebfbcb481dc
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617896"
---
# <a name="query-v2-http-response"></a>查詢 V2 HTTP 回應

如果狀態碼為200，則回應主體為 JSON 陣列。
陣列中的每個 JSON 物件稱為「_框架_」。

畫面格有幾種類型：

* [DataSetHeader](#datasetheader)
* [TableHeader](#tableheader)
* [TableFragment](#tablefragment)
* [TableProgress](#tableprogress)
* [TableCompletion](#tablecompletion)
* [資料表](#datatable)
* [DataSetCompletion](#datasetcompletion)

## <a name="datasetheader"></a>DataSetHeader 

`DataSetHeader`框架一律是資料集中的第一個，而且只會出現一次。

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

其中：

* `Version`這是通訊協定版本。 目前版本為 `v2.0`。
* `IsProgressive`這是一個布林值旗標，指出此資料集是否包含漸進式框架。 
   漸進式框架是下列其中一個：
   
     | Frame             | 描述                                    |
     |-------------------| -----------------------------------------------|
     | `TableHeader`     | 包含關於資料表的一般資訊   |
     | `TableFragment`   | 包含資料表的矩形資料分區 |
     | `TableProgress`   | 包含進度（以百分比表示）（0-100）       |
     | `TableCompletion` | 表示此框架是最後一個畫面格      |
        
    上述框架描述資料表。
    如果`IsProgressive`旗標未設定為 true，則會使用單一框架將集合中的每個資料表序列化：
* `DataTable`：包含用戶端在資料集中單一資料表所需的所有資訊。

## <a name="tableheader"></a>TableHeader

使用`results_progressive_enabled`選項設定為 true 的查詢可能會包含此框架。 在此資料表之後，用戶端可以預期交錯的`TableFragment`和`TableProgress`框架序列。 資料表的最後一個框架是`TableCompletion`。

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

其中：

* `TableId`這是資料表的唯一識別碼。
* `TableKind`是下列其中一項：

    * PrimaryResult
    * QueryCompletionInformation
    * QueryTraceLog
    * QueryPerfLog
    * TableOfContents
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`這是資料表的名稱。
* `Columns`這是描述資料表架構的陣列。

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

[這裡](../../query/scalar-data-types/index.md)描述支援的資料行類型。

## <a name="tablefragment"></a>TableFragment

此`TableFragment`框架包含資料表的矩形資料片段。 除了實際的資料之外，此框架也包含`TableFragmentType`屬性，告訴用戶端該如何處理片段。 附加至現有片段的片段，或加以取代。

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

其中：

* `TableId`這是資料表的唯一識別碼。
* `FieldCount`這是資料表中的資料行數目。
* `TableFragmentType`說明用戶端應該如何使用此片段。 
    `TableFragmentType`是下列其中一項：
    
    * DataAppend
    * DataReplace
      
* `Rows`是包含片段資料的二維陣列。

## <a name="tableprogress"></a>TableProgress

`TableProgress`畫面格可以與上面所`TableFragment`述的框架交錯。
其唯一目的是通知用戶端查詢的進度。

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

其中：

* `TableId`這是資料表的唯一識別碼。
* `TableProgress`這是以百分比表示的進度（0--100）。

## <a name="tablecompletion"></a>TableCompletion

此`TableCompletion`框架會標示資料表傳輸的結尾。 將不會再傳送與該資料表相關的任何畫面。

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

其中：

* `TableId`這是資料表的唯一識別碼。
* `RowCount`這是資料表中的資料列總數。

## <a name="datatable"></a>DataTable

`EnableProgressiveQuery`以旗標設定為 false 的查詢將不會包含任何框架（`TableHeader`、 `TableFragment`、 `TableProgress`和`TableCompletion`）。 相反地，資料集中的每個資料表都會使用包含客戶`DataTable`端所需之所有資訊的框架來傳輸，以讀取資料表。

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
    "Rows": Array,
}
```    

其中：

* `TableId`這是資料表的唯一識別碼。
* `TableKind`是下列其中一項：

    * PrimaryResult
    * QueryCompletionInformation
    * QueryTraceLog
    * QueryPerfLog
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`這是資料表的名稱。
* `Columns`是描述資料表架構的陣列，其中包括：

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

* `Rows`是包含資料表資料的二維陣列。

### <a name="the-meaning-of-tables-in-the-response"></a>回應中資料表的意義

* `PrimaryResult`-查詢的主要表格式結果。 針對每個[表格式運算式語句](../../query/tabularexpressionstatements.md)，會依序產生一或多個資料表，表示語句所產生的結果。 因為[批次](../../query/batches.md)和[分叉運算子](../../query/forkoperator.md)，所以可能會有多個這類資料表。
* `QueryCompletionInformation`-提供查詢本身執行的其他資訊，例如它是否已順利完成，以及查詢所使用的資源（類似 v1 回應中的 QueryStatus 資料表）。 
* `QueryProperties`-提供額外的值，例如用戶端視覺效果指示（例如，為了反映轉譯[運算子](../../query/renderoperator.md)中的資訊而發出）和[資料庫資料指標](../../management/databasecursor.md)資訊）。
* `QueryTraceLog`-效能追蹤記錄資訊（當`perftrace` [用戶端要求屬性](../netfx/request-properties.md)中的時傳回）設定為 true。

## <a name="datasetcompletion"></a>DataSetCompletion

`DataSetCompletion`框架是資料集中的最後一個。

```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

其中：

* `HasErrors`如果產生資料集時發生錯誤，則為 true。
* `Cancelled`如果導致資料集產生的要求在完成之前已取消，則為 true。 
* `OneApiErrors`只有在`HasErrors`為 true 時，才會傳回。 如需`OneApiErrors`格式的說明，請參閱[這裡](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md)的7.10.2 一節。
