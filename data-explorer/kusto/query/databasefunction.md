---
title: database （）（範圍函數）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料庫（）（範圍函數）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1bfe42e18cfe0bb424e933b266eb9861c7676cea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348576"
---
# <a name="database-scope-function"></a>database （）（scope 函數）

::: zone pivot="azuredataexplorer"

將查詢的參考變更為叢集範圍內的特定資料庫。 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

## <a name="syntax"></a>語法

`database(`*stringConstant*`)`

## <a name="arguments"></a>引數

* *stringConstant*：所參考之資料庫的名稱。 識別的資料庫可以是 `DatabaseName` 或 `PrettyName` 。 引數在執行查詢之前必須是_常數_，亦即，不能來自子查詢評估。

**備註**

* 如需存取遠端叢集和遠端資料庫的詳細資料，請參閱[cluster （）](clusterfunction.md) scope function。
* [這裡](cross-cluster-or-database-queries.md)提供跨叢集和跨資料庫查詢的詳細資訊

## <a name="examples"></a>範例

### <a name="use-database-to-access-table-of-other-database"></a>使用資料庫（）來存取其他資料庫的資料表。 

```kusto
database('Samples').StormEvents | count
```

|計數|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>在 let 語句內使用 database （） 

與上述相同的查詢可以重寫成使用內嵌函式（let 語句）來接收參數（ `dbName` 傳遞至 database （）函數）。

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

### <a name="use-database-inside-functions"></a>在函式內部使用資料庫（） 

您可以重新撰寫與上述相同的查詢，以在接收參數的函式中使用， `dbName` 這會傳遞至 database （）函數。

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

**注意：** 這類函式只能在本機使用，而不能用於跨叢集查詢。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
