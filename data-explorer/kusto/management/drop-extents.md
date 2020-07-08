---
title: 。放置範圍-Azure 資料總管
description: 本文說明 Azure 資料總管中的 [捨棄範圍] 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: dfa462ca82cd5e94adff77b3893b3b02d60c6cdc
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060597"
---
# <a name="drop-extents"></a>。放置範圍

從指定的資料庫或資料表中卸載範圍。

此命令有數個變體：在一個中，要卸載的範圍是由 Kusto 查詢指定。 在其他變異中，會使用以下所述的迷你語言來指定範圍。

針對具有所提供查詢所傳回之範圍的每個資料表，需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

> [!NOTE]
> 資料分區在 Kusto 中稱為**範圍**，而所有命令都會使用「範圍」或「範圍」做為同義字。
> 如需有關範圍的詳細資訊，請參閱[範圍（資料分區）總覽](extents-overview.md)。

## <a name="drop-extents-with-a-query"></a>使用查詢拖放範圍

使用 Kusto 查詢來放置指定的範圍。
傳回具有名為 "ExtentId" 之資料行的記錄集。

如果 `whatif` 使用，只會報告它們，而不會實際卸載。

### <a name="syntax"></a>Syntax

`.drop``extents`[ `whatif` ] <|*查詢*

## <a name="drop-a-specific-extent"></a>捨棄特定範圍

如果指定資料表名稱，則需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

如果未指定資料表名稱，則需要[資料庫系統管理員許可權](../management/access-control/role-based-authorization.md)。

### <a name="syntax"></a>Syntax

`.drop``extent` *ExtentId* [ `from` *TableName*]

## <a name="drop-specific-multiple-extents"></a>捨棄特定的多個範圍

如果指定資料表名稱，則需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

如果未指定資料表名稱，則需要[資料庫系統管理員許可權](../management/access-control/role-based-authorization.md)。

### <a name="syntax"></a>Syntax

`.drop``extents` `(` *ExtentId1* `,` .。。*ExtentIdN* `)`[ `from` *TableName*]

## <a name="drop-extents-by-specified-properties"></a>依指定的屬性放置範圍

命令支援會產生輸出的模擬模式，如同命令會執行，但未實際執行它的情況。 使用 `.drop-pretend` 取代 `.drop`。

如果指定資料表名稱，則需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

如果未指定資料表名稱，則需要[資料庫系統管理員許可權](../management/access-control/role-based-authorization.md)。

### <a name="syntax"></a>Syntax

`.drop``extents`[ `older` *N* （ `days`  |  `hours` ）] `from` （*TableName*  |  `all` `tables` ） [ `trim` `by` （ `extentsize`  |  `datasize` ） *N* （ `MB`  |  `GB`  |  `bytes` ）] [ `limit` *LimitCount*]

* `older`：只會捨棄超過*N*天/小時的範圍。
* `trim`：作業將會修剪資料庫中的資料，直到範圍總和符合所需的大小（MaxSize）為止。
* `limit`：作業將會套用至第一個*LimitCount*範圍。

## <a name="examples"></a>範例

### <a name="remove-all-extents-by-time-created"></a>依建立的時間移除所有範圍

從資料庫的所有資料表中移除超過10天前建立的所有範圍`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

### <a name="remove-some-extents-by-time-created"></a>依建立的時間移除某些範圍

移除資料表中的所有範圍 `Table1` ，且 `Table2` 其建立時間超過10天前

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

### <a name="emulation-mode-show-which-extents-would-be-removed-by-the-command"></a>模擬模式：顯示命令將移除的範圍

>[!NOTE]
>範圍識別碼參數不適用於此命令。

```kusto
.drop-pretend extents older 10 days from all tables
```

### <a name="remove-all-extents-from-testtable"></a>從 ' TestTable ' 移除所有範圍

```kusto
.drop extents from TestTable
```

## <a name="return-output"></a>傳回輸出

|輸出參數 |類型 |Description 
|---|---|---
|ExtentId |String |因為命令而卸載的 ExtentId
|TableName |String |資料表名稱，其中的範圍所屬  
|CreatedOn |Datetime |時間戳記，其中包含最初建立範圍時的相關資訊
 
## <a name="sample-output"></a>範例輸出

|範圍識別碼 |資料表名稱 |建立於 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12：48：49.4298178
