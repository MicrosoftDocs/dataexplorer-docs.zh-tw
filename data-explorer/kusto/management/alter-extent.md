---
title: 。 alter 範圍標記-Azure 資料總管
description: 本文說明 Azure 資料總管中的 alter 範圍命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 00c4cfbb4b6415afcd68e8e41864ca4a68cc097e
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060568"
---
# <a name="alter-extent-tags"></a>。 alter 範圍標記

命令會在特定資料庫的內容中執行。 它會改變查詢所傳回之所有範圍的指定[範圍標記](extents-overview.md#extent-tagging)。

您可以使用 Kusto 查詢來指定要改變的範圍和標籤，以傳回具有名為 "ExtentId" 之資料行的記錄集。

需要所有相關資料表的[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

> [!NOTE]
> 資料分區在 Kusto 中稱為**範圍**，而所有命令都會使用「範圍」或「範圍」做為同義字。
> 如需有關範圍的詳細資訊，請參閱[範圍（資料分區）總覽](extents-overview.md)。

## <a name="syntax"></a>語法

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` '*TagN*'] `)`  <|  *查詢*

`async`（選擇性）：以非同步方式執行命令。
   * 傳回作業識別碼（Guid）。 
   * 作業的狀態可以是 [受監視]。 使用 [[顯示作業](operations.md#show-operations)] 命令。
   * 您可以取得成功執行的結果。 使用 [[顯示作業詳細資料](operations.md#show-operation-details)] 命令。

## <a name="restrictions"></a>限制

所有範圍都必須在內容資料庫中，而且必須屬於相同的資料表。

## <a name="return-output"></a>傳回輸出

|輸出參數 |類型 |描述|
|---|---|---|
|OriginalExtentId |字串 |原始範圍的唯一識別碼（GUID），其標記已修改。 範圍會在作業中卸載。|
|ResultExtentId |字串 |已修改標記之結果範圍的唯一識別碼（GUID）。 範圍會建立並加入做為作業的一部分。 失敗時-「失敗」。|
|ResultExtentTags |字串 |已標記結果範圍的標記集合，如果作業失敗則為 "null"。|
|詳細資料 |字串 |如果作業失敗，則包含失敗詳細資料。|

## <a name="examples"></a>範例

### <a name="alter-tags"></a>改變標記 

將資料表中所有範圍的標記變更 `MyTable` 為`MyTag`

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

### <a name="alter-tags-of-all-extents"></a>改變所有範圍的標記

改變數據表中所有範圍的卷 `MyTable` 標， `drop-by:MyTag` 並將其標記為 `drop-by:MyNewTag` 和`MyOtherNewTag`

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

## <a name="sample-output"></a>範例輸出

|OriginalExtentId |ResultExtentId | ResultExtentTags | 詳細資料
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | drop by： MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | drop by： MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | drop by： MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | drop by： MyNewTag MyOtherNewTag| 
