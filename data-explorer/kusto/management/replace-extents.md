---
title: 。取代範圍-Azure 資料總管
description: 本文說明 Azure 資料總管中的 [取代範圍] 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: cd193cb370136fd7f14a8892f157a895a1d7ad50
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060609"
---
# <a name="replace-extents"></a>.replace extents

此命令會在特定資料庫的內容中執行。
它會將指定的範圍從其來源資料表移到目的地資料表，然後從目的地資料表中卸載指定的範圍。
所有的 drop 和 move 作業都是在單一交易中完成。

需要來源和目的地資料表的[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

> [!NOTE]
> 資料分區在 Kusto 中稱為**範圍**，而所有命令都會使用「範圍」或「範圍」做為同義字。
> 如需有關範圍的詳細資訊，請參閱[範圍（資料分區）總覽](extents-overview.md)。

## <a name="syntax"></a>語法

`.replace`[ `async` ] `extents` `in` `table` *DestinationTableName*要 `<| 
{` *從資料表查詢中卸載的範圍查詢*，以 `},{` *將範圍移至資料表*`}`

* `async`（選擇性）：以非同步方式執行命令。
    * 傳回作業識別碼（Guid）。
    * 作業的狀態可以是 [受監視]。 使用 [[顯示作業](operations.md#show-operations)] 命令。
    * 可以抓取成功執行的結果。 使用 [[顯示作業詳細資料](operations.md#show-operation-details)] 命令。

若要指定應該卸載或移動的範圍，請使用兩個查詢的其中一個。
* *查詢要從資料表*卸載的範圍：此查詢的結果會指定應該從目的地資料表中卸載的範圍識別碼。
* *查詢要移到資料表的範圍*：此查詢的結果會指定來源資料表中應該移至目的地資料表的範圍識別碼。

這兩個查詢都應該傳回具有名為 "ExtentId" 之資料行的記錄集。

## <a name="restrictions"></a>限制

* 來源和目的地資料表都必須在內容資料庫中。
* 查詢所指定*要從資料表中卸載之範圍*的所有範圍，都應該屬於目的地資料表。
* 來源資料表中的所有資料行都應該存在於具有相同名稱和資料類型的目的地資料表中。

## <a name="return-output-for-sync-execution"></a>傳回輸出（用於同步執行）

輸出參數 |類型 |描述
---|---|---
OriginalExtentId |字串 |來源資料表中已移至目的地資料表的原始範圍的唯一識別碼（GUID），或已卸載之目的地資料表中的範圍。
ResultExtentId |字串 |已從來源資料表移到目的地資料表的結果範圍唯一識別碼（GUID）。 如果已從目的地資料表中卸載範圍，則為空白。 失敗時：「失敗」。
詳細資料 |字串 |如果作業失敗，則包含失敗詳細資料。

> [!NOTE]
> 如果*要從資料表*查詢中卸載的範圍所傳回的範圍不存在於目的地資料表中，此命令將會失敗。 如果在執行 replace 命令之前合併範圍，就可能會發生這種情況。
> 若要確保命令在遺失的範圍中失敗，請檢查查詢是否傳回預期的 ExtentIds。 如果要卸載的範圍不存在於資料表*MyOtherTable*中，以下的範例 #1 將會失敗。 不過，#2 的範例會成功，即使要卸載的範圍不存在，因為卸載的查詢不會傳回任何範圍識別碼。

## <a name="examples"></a>範例

### <a name="move-all-extents-from-two-tables"></a>從兩個數據表移動所有範圍 

將兩個特定資料表（、）中的所有範圍移 `MyTable1` `MyTable2` 至資料表 `MyOtherTable` ，並將所有範圍放在 `MyOtherTable` 標記的中 `drop-by:MyTag` ：

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

#### <a name="sample-output"></a>範例輸出

|OriginalExtentId |ResultExtentId |詳細資料
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

### <a name="move-all-extents-from-one-table-to-another-drop-specific-extent"></a>將一個資料表的所有範圍移至另一個，捨棄特定的範圍

將一個特定 tavle （）中的所有範圍移 `MyTable1` 至資料表 `MyOtherTable` ，並依其識別碼卸載中的特定範圍 `MyOtherTable` ：

```kusto
.replace extents in table MyOtherTable <|
    {
        print ExtentId = "2cca5844-8f0d-454e-bdad-299e978be5df"
    },
    {
        .show table MyTable1 extents 
    }
```

```kusto
.replace extents in table MyOtherTable  <|
    {
        .show table MyOtherTable extents
        | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) 
    },
    {
        .show table MyTable1 extents 
    }
```

### <a name="implement-an-idempotent-logic"></a>執行等冪邏輯

執行等冪邏輯， `t_dest` 只有在有範圍要從資料表移到資料表時，Kusto 才會從資料表中卸載範圍 `t_source` `t_dest` ：

```kusto
.replace async extents in table t_dest <|
{
    let any_extents_to_move = toscalar( 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize count() > 0
    );
    let extents_to_drop =
        t_dest
        | where any_extents_to_move and extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_drop
},
{
    let extents_to_move = 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_move
}
```
