---
title: 。移動範圍-Azure 資料總管
description: 本文說明 Azure 資料總管中的 [移動範圍] 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 994f7077b1583317320cc561b2d5a5ae1cbe829f
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060594"
---
# <a name="move-extents"></a>。移動範圍

此命令會在特定資料庫的內容中執行。 它會將指定的範圍從來源資料表移到目的地資料表。

此命令需要來源和目的地資料表的[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

> [!NOTE]
> 資料分區在 Kusto 中稱為**範圍**，而所有命令都會使用「範圍」或「範圍」做為同義字。
> 如需有關範圍的詳細資訊，請參閱[範圍（資料分區）總覽](extents-overview.md)。

## <a name="syntax"></a>Syntax

`.move`[ `async` ] `extents` `all` `from` `table` *SourceTableName* `to` `table` *DestinationTableName*

`.move`[ `async` ] `extents` `(` *GUID1* [ `,` *GUID2* ... `)` `from` ] `table`*SourceTableName* `to``table` *DestinationTableName* 

`.move`[ `async` ] `extents` `to` `table` *DestinationTableName*  <|  *查詢*

`async`（選擇性）。 以非同步方式執行命令。 
   * 傳回作業識別碼（Guid）。
   * 作業的狀態可以是 [受監視]。 使用 [[顯示作業](operations.md#show-operations)] 命令。
   * 可以抓取成功執行的結果。 使用 [[顯示作業詳細資料](operations.md#show-operation-details)] 命令。

有三種方式可指定要移動的範圍：
* 移動特定資料表的所有範圍。
* 明確指定來源資料表中的範圍識別碼。
* 提供查詢，其結果會指定來源資料表中的範圍識別碼。

## <a name="restrictions"></a>限制

* 來源和目的地資料表都必須在內容資料庫中。
* 來源資料表中的所有資料行都應該存在於具有相同名稱和資料類型的目的地資料表中。

## <a name="specify-extents-with-a-query"></a>使用查詢指定範圍

```kusto
.move extents to table TableName <| ...query...
```

範圍的指定方式是使用 Kusto 查詢，其會傳回具有名為*ExtentId*之資料行的記錄集。

## <a name="return-output-for-sync-execution"></a>傳回輸出（用於同步執行）

輸出參數 |類型 |Description
---|---|---
OriginalExtentId |字串 |來源資料表中原始範圍的唯一識別碼（GUID），已移至目的地資料表。
ResultExtentId |字串 |已從來源資料表移到目的地資料表的結果範圍唯一識別碼（GUID）。 失敗時-「失敗」。
詳細資料 |字串 |包含失敗詳細資料，以防作業失敗。

## <a name="examples"></a>範例

### <a name="move-all-extents"></a>移動所有範圍 

將資料表中的所有範圍移 `MyTable` 至資料表 `MyOtherTable` ：

```kusto
.move extents all from table MyTable to table MyOtherTable
```

### <a name="move-two-specific-extents"></a>移動兩個特定範圍 

將兩個特定範圍（依據其範圍識別碼）從資料表移 `MyTable` 至資料表 `MyOtherTable` ：

```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

### <a name="move-all-extents-from-specific-tables"></a>從特定資料表移動所有範圍 

將特定 tavles （，）的所有範圍移 `MyTable1` `MyTable2` 至資料表 `MyOtherTable` ：

```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

## <a name="sample-output"></a>範例輸出

|OriginalExtentId |ResultExtentId| 詳細資料
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 
