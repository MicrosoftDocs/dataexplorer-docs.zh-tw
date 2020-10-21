---
title: Alias 語句-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Alias 語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 822c8eccf50dc30fd3f56f4402c10a9fafb34084
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248291"
---
# <a name="alias-statement"></a>Alias 陳述式

::: zone pivot="azuredataexplorer"

Alias 語句可讓您定義資料庫的別名，稍後可在相同的查詢中使用。

當您使用數個叢集，但想要看起來像是使用較少的叢集時，這非常有用。
您必須根據下列語法定義別名，其中 *clustername* 和 *databasename* 是現有的有效實體。

## <a name="syntax"></a>語法

`alias` 資料庫 [*' DatabaseAliasName '*] 叢集 `=` ( "HTTPs://*clustername*. kusto.windows.net:443" ) . 資料庫 ( "*databasename*" ) 

`alias` 資料庫 *DatabaseAliasName*叢集 `=` ( "HTTPs://*clustername*. kusto.windows.net:443" ) . 資料庫 ( "*databasename*" ) 

* *' DatabaseAliasName '* 可以是現有的名稱或新名稱。
* 對應的叢集 uri 和對應的資料庫名稱必須出現在雙引號內 ( ") 或單引號 ( ' ) 

## <a name="examples"></a>範例

```kusto
alias database["wiki"] = cluster("https://somecluster.kusto.windows.net:443").database("somedatabase");
database("wiki").PageViews | count 
```

```kusto
alias database Logs = cluster("https://othercluster.kusto.windows.net:443").database("otherdatabase");
database("Logs").Traces | count 
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
