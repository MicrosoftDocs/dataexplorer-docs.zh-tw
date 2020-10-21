---
title: '叢集 ( # A1 (範圍函數) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的叢集 ( # A1 (範圍函數) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b2f9dd3fd3eede1f6d527c97b905353f9a331620
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253069"
---
# <a name="cluster-scope-function"></a>叢集 ( # A1 (範圍函數) 

::: zone pivot="azuredataexplorer"

變更查詢對遠端叢集的參考。 

```kusto
cluster('help').database('Sample').SomeTable
```

## <a name="syntax"></a>語法

`cluster(`*stringConstant*`)`

## <a name="arguments"></a>引數

* *stringConstant*：所參考叢集的名稱。 叢集名稱可以是完整的 DNS 名稱，也可以是將加上尾碼的字串 `.kusto.windows.net` 。 引數必須是查詢執行之前的 _常數_ ，也就是不能來自子查詢評估。

**備註**

* 若要存取相同叢集中的資料庫，請使用 [資料庫 ( # B1 ](databasefunction.md) 函數。
* [這裡](cross-cluster-or-database-queries.md)提供跨叢集和跨資料庫查詢的詳細資訊  

## <a name="examples"></a>範例

### <a name="use-cluster-to-access-remote-cluster"></a>使用叢集 ( # A1 來存取遠端叢集 

下一個查詢可以在任何 Kusto 叢集上執行。

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|計數|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>在 let 語句內使用叢集 ( # A1 

您可以重寫上述相同的查詢，以使用內嵌函式 (let 語句) 來接收參數， `clusterName` 該參數會傳遞至叢集 ( # A3 函式中。

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

### <a name="use-cluster-inside-functions"></a>在函式中使用叢集 ( # A1 

您可以重寫上述相同的查詢，以在接收參數的函式中使用，而該函 `clusterName` 式會傳遞至叢集 ( # A1 函數。

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**注意：** 這類函數只能用在本機，而不能在跨叢集查詢中使用。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
