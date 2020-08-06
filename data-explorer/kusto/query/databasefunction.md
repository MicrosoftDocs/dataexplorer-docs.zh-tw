---
title: '資料庫 ( # A1 (scope 函數) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的資料庫 ( # A1 (scope 函數) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6511006373cd1f6245a0dcc04537f3994183d63e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803755"
---
# <a name="database-scope-function"></a>資料庫 ( # A1 (scope 函數) 

::: zone pivot="azuredataexplorer"

將查詢的參考變更為叢集範圍內的特定資料庫。 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

> [!NOTE]
> * 如需詳細資訊，請參閱[跨資料庫和跨叢集查詢](cross-cluster-or-database-queries.md)。
> * 如需存取遠端叢集和遠端資料庫的詳細資料，請參閱[cluster ( # B1](clusterfunction.md) scope function。

## <a name="syntax"></a>語法

`database(`*stringConstant*`)`

## <a name="arguments"></a>引數

* *stringConstant*：所參考之資料庫的名稱。 識別的資料庫可以是 `DatabaseName` 或 `PrettyName` 。 引數在執行查詢之前必須是_常數_，亦即，不能來自子查詢評估。

## <a name="examples"></a>範例

### <a name="use-database-to-access-table-of-other-database"></a>使用資料庫 ( # A1 來存取其他資料庫的資料表

```kusto
database('Samples').StormEvents | count
```

|計數|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>在 let 語句內部使用資料庫 ( # A1 

與上述相同的查詢可以重寫為使用內嵌函式 (let 語句) 接收參數（ `dbName` 傳遞至資料庫 ( # A3 函數）。

```kusto
let foo = (dbName:string)
{
    database(dbName).StormEvents | count
};
foo('help')
```

|計數|
|---|
|59066|

### <a name="use-database-inside-functions"></a>在函式內部使用資料庫 ( # A1 

您可以重新撰寫與上述相同的查詢，以便在接收參數的函式中使用，而此參數 `dbName` 會傳遞至資料庫 ( # A1 函數。

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

> [!NOTE]
> 這類函數只能在本機使用，而不能用於跨叢集查詢。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
