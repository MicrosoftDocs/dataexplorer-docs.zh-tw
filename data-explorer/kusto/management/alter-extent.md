---
title: . 改變範圍標籤-Azure 資料總管
description: 本文說明 Azure 資料總管中的 alter 延伸區命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 61dba69c3ec40ec13960e9ddf266e47fe88ef3c6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321738"
---
# <a name="alter-extent-tags"></a>. 改變範圍標記

此命令會在特定資料庫的內容中執行。 它會改變查詢所傳回之所有範圍的指定 [範圍標記](extents-overview.md#extent-tagging) 。

範圍和要改變的標記會使用 Kusto 查詢來指定，此查詢會傳回具有名為 "ExtentId" 之資料行的記錄集。

需要所有相關資料表的 [Table admin 許可權](../management/access-control/role-based-authorization.md) 。

> [!NOTE]
> 資料分區在 Kusto 中稱為 **延伸區** ，而且所有命令都會使用「範圍」或「範圍」做為同義字。
> 如需範圍的詳細資訊，請參閱 [分區的範圍 (資料) 總覽](extents-overview.md)。

## <a name="syntax"></a>語法

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` '*TagN*'] `)`  <|  *查詢*

`async` (選擇性) ：以非同步方式執行命令。
   * 傳回 (Guid) 的作業識別碼。 
   * 可以監視作業的狀態。 使用 [`.show operations`](operations.md#show-operations) 命令。
   * 您可以取得成功執行的結果。 使用 [`.show operation details`](operations.md#show-operation-details) 命令。

## <a name="restrictions"></a>限制

所有範圍都必須在內容資料庫中，而且必須屬於相同的資料表。

## <a name="return-output"></a>傳回輸出

|輸出參數 |類型 |描述|
|---|---|---|
|OriginalExtentId |字串 |針對其標記已修改的原始範圍， (GUID) 的唯一識別碼。 此範圍會在作業過程中卸載。|
|ResultExtentId |字串 |具有已修改標記之結果範圍 (GUID) 的唯一識別碼。 範圍會建立，並新增為作業的一部分。 失敗時-「失敗」。|
|ResultExtentTags |字串 |標記結果範圍標記的集合，如果作業失敗，則為 "null"。|
|詳細資料 |字串 |如果作業失敗，則包含失敗詳細資料。|

## <a name="examples"></a>範例

### <a name="alter-tags"></a>改變標記 

將資料表中所有範圍的標記變更 `MyTable` 為 `MyTag`

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

### <a name="alter-tags-of-all-extents"></a>改變所有範圍的標記

改變數據表中所有範圍的卷 `MyTable` 標， `drop-by:MyTag` 並將其標記為 `drop-by:MyNewTag` 和 `MyOtherNewTag`

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

## <a name="sample-output"></a>範例輸出

|OriginalExtentId |ResultExtentId | ResultExtentTags | 詳細資料
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | 下拉式： MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | 下拉式： MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | 下拉式： MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | 下拉式： MyNewTag MyOtherNewTag| 
