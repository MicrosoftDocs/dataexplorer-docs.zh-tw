---
title: '資料庫 ( # A1 (範圍函數) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的資料庫 ( # A1 (範圍函數) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 24abca1d6ff930796a4f57b3f21de4552405112c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252465"
---
# <a name="database-scope-function"></a>資料庫 ( # A1 (範圍函數) 

::: zone pivot="azuredataexplorer"

將查詢的參考變更為叢集範圍內的特定資料庫。 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

> [!NOTE]
> * 如需詳細資訊，請參閱 [跨資料庫和跨叢集查詢](cross-cluster-or-database-queries.md)。
> * 如需存取遠端叢集和遠端資料庫的詳細資料，請參閱 [cluster ( # B1 ](clusterfunction.md) 範圍函數。

## <a name="syntax"></a>語法

`database(`*stringConstant*`)`

## <a name="arguments"></a>引數

* *stringConstant*：所參考資料庫的名稱。 識別的資料庫可以是 `DatabaseName` 或 `PrettyName` 。 引數必須是查詢執行之前的 _常數_ ，也就是不能來自子查詢評估。

## <a name="examples"></a>範例

### <a name="use-database-to-access-table-of-other-database"></a>使用資料庫 ( # A1 來存取其他資料庫的資料表

```kusto
database('Samples').StormEvents | count
```

|計數|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>在 let 語句內使用資料庫 ( # A1 

您可以重寫上述相同的查詢，以使用內嵌函式 (let 語句) 來接收參數， `dbName` 該參數會傳遞至資料庫 ( # A3 函數。

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

### <a name="use-database-inside-functions"></a>在函式中使用資料庫 ( # A1 

您可以重寫上述相同的查詢，以在接收參數的函式中使用， `dbName` 該函式會傳遞至資料庫 ( # A1 函數。

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

> [!NOTE]
> 這類函數只能用在本機，而不能在跨叢集查詢中使用。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
