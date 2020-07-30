---
title: Alias 語句-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Alias 語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5e243984bd6a011b8de224d2c9cdd0108ab1b38f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349749"
---
# <a name="alias-statement"></a>Alias 陳述式

::: zone pivot="azuredataexplorer"

Alias 語句可讓您定義資料庫的別名，稍後可以在相同的查詢中使用。

當您使用數個叢集，但想要像是在較少的叢集上運作時，這會很有用。
別名必須根據下列語法來定義，其中*clustername*和*databasename*是現有和有效的實體。

## <a name="syntax"></a>語法

`alias`database [*' DatabaseAliasName '*] 叢集 `=` （"HTTPs：//*clustername*. kusto"）. database （"*databasename*"）

`alias`資料庫*DatabaseAliasName*叢集 `=` （"HTTPs：//*clustername*. kusto"）. 資料庫（"*databasename*"）

* *' DatabaseAliasName '* 可以是現有的名稱或新名稱。
* 對應的叢集 uri 和對應的資料庫名稱必須出現在雙引號（"）或單引號（'）內

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
