---
title: 查詢 V2 HTTP 回應 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的查詢 V2 HTTP 回應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cca9b8381c7c59993c1e9071c46f34c1754d2429
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524305"
---
# <a name="query-v2-http-response"></a>查詢 V2 HTTP 回應

如果狀態代碼為 200,則回應正文為 JSON 陣列。
陣列中的每個 JSON 物件都稱為_幀_。

有 7 種類型的幀:

1. 資料集標題
2. 表標題
3. 表碎片
4. 表進度
5. 表完成
6. DataTable
7. 資料集完成

## <a name="datasetheader"></a>資料集標題 

這始終是數據集中的第一幀,並正好顯示一次。

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

其中：

1. `Version`保留協定版本。 目前版本為 `v2.0`。
2. `IsProgressive`是一個布爾標誌,指示此數據集是否包含漸進幀。 漸進式框架是下列框架之一:
    1. `TableHeader`: 包含有關表的一般資訊
    2. `TableFragment`: 包含表的直腸資料分片
    3. `TableProgress`: 包含百分比的進度 (0-100)
    4. `TableCompletion`:標記這是表的最後一幀。
        
    上面的四個幀一起使用來描述表。
    如果未設定此標誌,則集中的每個表都將使用單個幀進行序列化:
      1. `DataTable`:包含用戶端所需的有關數據集中單個表的所有資訊。


## <a name="tableheader"></a>表標題

使用`results_progressive_enabled`選項設定為 true 發出的查詢可能包括此幀。 在此表之後,用戶端應期望與`TableFragment``TableProgress`幀的交錯順序,後跟幀`TableCompletion`。 之後,將不再發送與該表相關的幀。

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

其中：

1. `TableId`是表的唯一 ID。
2. `TableKind`是表的種類,可以是以下項之一:

      * 主要結果
      * 查詢完成資訊
      * 查詢追蹤紀錄
      * 查詢PerfLog
      * TableOfContents
      * QueryProperties
      * 查詢計劃
      * Unknown
3. `TableName`是表的名稱。
4. `Columns`是描述表架構的陣列:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
[此處](../../query/scalar-data-types/index.md)介紹了支援的列類型。

## <a name="tablefragment"></a>表碎片

此框架包含表的矩形數據片段。 除了實際數據之外,此幀還包含一個`TableFragmentType`屬性,該屬性告訴用戶端如何處理片段(它可以追加到現有片段,也可以將它們全部替換在一起)。

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

其中：

1. `TableId`是表的唯一 ID。
2. `FieldCount`是表格欄數
3. `TableFragmentType`描述客戶端應如何處理此片段。 可以是下列其中一項：
      * 資料應用
      * 資料取代
4. `Rows`是包含片段數據的二維陣列。

## <a name="tableprogress"></a>表進度

此幀可以與上述`TableFragment`幀交錯。
它的唯一目的是通知客戶端查詢進度。

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

其中：

1. `TableId`是表的唯一 ID。
2. `TableProgress`以百分比 (0-100) 表示進度。

## <a name="tablecompletion"></a>表完成

幀`TableCompletion`標記表傳輸的結束。 將不再發送與該表相關的幀。

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

其中：

1. `TableId`是表的唯一 ID。
2. `RowCount`是表中的最後行數。

## <a name="datatable"></a>DataTable

`EnableProgressiveQuery`將標誌設定為 false 時發出的查詢將不包括之前 4 幀`TableHeader`中的`TableFragment``TableProgress`任何`TableCompletion`幀 ( 和 。 相反,數據集中的每個表都將使用單個幀(`DataTable`幀)傳輸,該幀包含用戶端讀取表所需的所有資訊。

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

1. `TableId`是表的唯一 ID。
2. `TableKind`是表的種類,可以是以下項之一:

      * 主要結果
      * 查詢完成資訊
      * 查詢追蹤紀錄
      * 查詢PerfLog
      * QueryProperties
      * 查詢計劃
      * Unknown
3. `TableName`是表的名稱。
4. `Columns`是描述表架構的陣列:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
4. `Rows`是包含表數據的二維陣列。

### <a name="the-meaning-of-tables-in-the-response"></a>回應中表的含義

* `PrimaryResult`- 查詢的主要表格結果。 對於每個[表格表達式語句](../../query/tabularexpressionstatements.md),按順序發出一個或多個表,表示該語句生成的結果(由於[批處理](../../query/batches.md)和[分叉運算符](../../query/forkoperator.md),可以有多個此類表)。
* `QueryCompletionInformation`- 提供有關查詢本身執行的其他資訊,例如查詢本身是否成功完成,以及查詢消耗的資源(類似於 v1 回應中的 QueryStatus 表)。 
* `QueryProperties`- 提供其他值,如用戶端可視化指令(例如,用於反映[呈現運算符](../../query/renderoperator.md)中的資訊和[資料庫游標](../../management/databasecursor.md)資訊)。
* `QueryTraceLog`- 性能追蹤日誌資訊(在[用戶端請求屬性](../netfx/request-properties.md)中設置 perftrace 時返回)。

## <a name="datasetcompletion"></a>資料集完成

這是數據集中的最後一幀。
```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

其中：

1. `HasErrors`如果生成數據集時出現任何錯誤,則為 true。
2. `Cancelled`如果導致生成數據集的請求中途被取消,則為 true。 
3. `OneApiErrors`僅在為`HasErrors`true 時傳輸。 有關`OneApiErrors`格式的說明,請參閱[此處](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md)的第 7.10.2 節。