---
title: 叢集() (範圍函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的群集(範圍函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c5f537b47006af4035c9db26388c1d4110c69b55
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766097"
---
# <a name="cluster-scope-function"></a>叢集 () (range)

::: zone pivot="azuredataexplorer"

將查詢的引用更改為遠端群集。 

```kusto
cluster('help').database('Sample').SomeTable
```

**語法**

`cluster(`*字串常數*`)`

**引數**

* *字串常量*:被引用的群集的名稱。 叢集名稱可以是完全限定的 DNS 名稱,也可以是將隨`.kusto.windows.net`後綴的 字串。 在執行查詢之前,參數必須是_恆定的_,即不能來自子查詢計算。

**注意事項**

* 要存取同一群集中的資料庫 - 使用[資料庫()](databasefunction.md)函數。
* 有關跨群集和跨資料庫查詢的詳細資訊[,可在此處](cross-cluster-or-database-queries.md)提供  

## <a name="examples"></a>範例

### <a name="use-cluster-to-access-remote-cluster"></a>使用叢集 () 存取遠端叢集 

下一個查詢可以在任何 Kusto 群集上運行。

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>在 let 語片中使用叢集() 

可以重寫與上述相同的查詢,以使用接收參數的內聯函數(let 語句),該函`clusterName`數將傳遞到群集()函數中。

```kusto
let foo = (clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
foo('help')
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-functions"></a>在函數內使用群集() 

可以重寫與上述相同的查詢,以用於接收參數的函數, 該`clusterName`參數將傳遞到群集() 函數中。

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**注意:** 此類函數只能在本地使用,不能在跨群集查詢中使用。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
