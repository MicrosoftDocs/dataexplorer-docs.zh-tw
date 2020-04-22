---
title: 別名語句 - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的別名語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c6c689ab6daacebe1cd20742b199c8b9cc299245
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766084"
---
# <a name="alias-statement"></a>Alias 陳述式

::: zone pivot="azuredataexplorer"

別名語句允許為資料庫定義別名,這些別名可在以後在同一查詢中使用。

當處理多個群集時,在嘗試在較少的群集上或僅在一個群集上顯示為工作時,這非常有用。
別名必須根據以下語法定義,其中*群集名稱*和*資料庫名稱*必須是現有有效的實體。

**語法**

`alias`資料庫=*"資料庫別名"* `=` = 群集("HTTT. a.kusto.windows.net:443"資料庫("*資料庫名稱*")*clustername*

`alias`*資料庫資料庫 AliasName*`=`叢集(「HTTPs://*群集名稱*.kusto.windows.net:443"資料庫("*資料庫名稱*")

* *"資料庫別名"* 可以是軸線名稱或新名稱。
* 映射的群集 uri 和映射的資料庫名稱必須出現在雙引號(「)或單引號(「)內

**範例**

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

Azure 監視器不支援此功能

::: zone-end
