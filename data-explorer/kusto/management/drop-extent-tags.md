---
title: . 捨棄範圍標籤-Azure 資料總管
description: 本文說明 Azure 資料總管中的 [捨棄範圍標記] 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 45e7d0abf42e613a9d197371dcc374fe4ac11fed
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321007"
---
# <a name="drop-extent-tags"></a>.drop extent 標記

此命令會在特定資料庫的內容中執行。 它會從資料庫和資料表中的所有或特定範圍卸載特定的 [範圍標記](extents-overview.md#extent-tagging) 。  

> [!NOTE]
> 資料分區在 Kusto 中稱為 **延伸區** ，而且所有命令都會使用「範圍」或「範圍」做為同義字。
> 如需範圍的詳細資訊，請參閱 [分區的範圍 (資料) 總覽](extents-overview.md)。

有兩種方式可以指定要從哪些範圍中移除哪些標記：

* 明確指定應該從指定資料表中的所有範圍移除的標記。
* 提供一個查詢，其結果會指定資料表中的範圍識別碼，以及針對每個範圍（應移除的標記）。

## <a name="syntax"></a>語法

`.drop` [ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` '*TagN*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *查詢*

`async` (選擇性) ：以非同步方式執行命令。
   * 傳回 (Guid) 的作業識別碼。
   * 可以監視作業的狀態。 使用 [`.show operations`](operations.md#show-operations) 命令。
   * 使用 [`.show operation details`](operations.md#show-operation-details) 命令可取得成功執行的結果。

## <a name="restrictions"></a>限制

所有範圍都必須在內容資料庫中，而且必須屬於相同的資料表。

## <a name="specify-extents-with-a-query"></a>使用查詢指定範圍

範圍和要卸載的標記是使用 Kusto 查詢指定的。 它會傳回具有名為 "ExtentId" 之資料行的記錄集，以及名為 "Tags" 的資料行。

> [!NOTE]
> 使用 [Kusto .net 用戶端程式庫](../api/netfx/about-kusto-data.md)時，下列方法會產生必要的命令：
> * `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
> * `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

需要所有相關來源和目的地資料表的「 [資料表管理員」許可權](../management/access-control/role-based-authorization.md) 。

### <a name="syntax-for-drop-extent-tags-in-query"></a>的語法。在查詢中放置範圍標記

```kusto 
.drop extent tags <| ...query...
```

### <a name="return-output"></a>傳回輸出

輸出參數 |類型 |描述 
---|---|---
OriginalExtentId |字串 |針對其標記已修改的原始範圍， (GUID) 的唯一識別碼。 此範圍會在作業過程中卸載。
ResultExtentId |字串 |具有已修改標記之結果範圍 (GUID) 的唯一識別碼。 範圍會建立，並新增為作業的一部分。 失敗時-「失敗」。
ResultExtentTags |字串 |標記結果範圍標記的集合（如果有的話）; 如果作業失敗，則為 "null"。
詳細資料 |字串 |如果作業失敗，則包含失敗詳細資料。

## <a name="examples"></a>範例

### <a name="drop-one-tag"></a>放置一個標記

`drop-by:Partition000`從已標記的資料表中的任何範圍中，卸載標記：

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

### <a name="drop-several-tags"></a>捨棄數個標記

從以其中之一 `drop-by:20160810104500` `a random tag` 標記的 `drop-by:20160810` 資料表中的任何範圍，卸載標記、和。

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

### <a name="drop-all-drop-by-tags"></a>捨棄所有 `drop-by` 標記 

`drop-by`從資料表中的範圍卸載所有標記 `MyTable` ：

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

### <a name="drop-all-tags-matching-specific-regex"></a>捨棄符合特定 RegEx 的所有標記 

`drop-by:StreamCreationTime_20160915(\d{6})`從資料表的範圍中，卸載符合 RegEx 的所有標記 `MyTable` ：

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

## <a name="sample-output"></a>範例輸出

|OriginalExtentId |ResultExtentId | ResultExtentTags | 詳細資料
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |
