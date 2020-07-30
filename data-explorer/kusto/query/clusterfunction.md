---
title: cluster （）（範圍函數）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 cluster （）（範圍函數）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3e1f74d6605b4916a2718a00fd252141060d748f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348882"
---
# <a name="cluster-scope-function"></a>cluster （）（scope 函數）

::: zone pivot="azuredataexplorer"

將查詢的參考變更為遠端叢集。 

```kusto
cluster('help').database('Sample').SomeTable
```

## <a name="syntax"></a>語法

`cluster(`*stringConstant*`)`

## <a name="arguments"></a>引數

* *stringConstant*：所參考的叢集名稱。 叢集名稱可以是完整的 DNS 名稱，或是尾碼為的字串 `.kusto.windows.net` 。 引數在查詢執行之前必須是_常數_，亦即，不能來自子查詢評估。

**備註**

* 用於存取相同叢集內的資料庫-use [database （）](databasefunction.md)函數。
* [這裡](cross-cluster-or-database-queries.md)提供跨叢集和跨資料庫查詢的詳細資訊  

## <a name="examples"></a>範例

### <a name="use-cluster-to-access-remote-cluster"></a>使用 cluster （）存取遠端叢集 

下一個查詢可以在任何 Kusto 叢集上執行。

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|計數|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>使用 let 語句中的 cluster （） 

與上述相同的查詢可以重寫成使用內嵌函式（let 語句）來接收參數（ `clusterName` 傳遞至 cluster （）函數）。

```kusto
let foo = (clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
foo('help')
```

|計數|
|---|
|59066|

### <a name="use-cluster-inside-functions"></a>在函式內部使用 cluster （） 

與上述相同的查詢可以重寫，以便在接收參數的函式中使用，該函數 `clusterName` 會傳遞至 cluster （）函數。

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**注意：** 這類函式只能在本機使用，而不能用於跨叢集查詢。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
