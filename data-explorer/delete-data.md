---
title: 從 Azure 資料總管刪除資料
description: 本文介紹 Azure 資料資源管理器中的刪除方案,包括清除、刪除擴展盤區和基於保留的刪除。
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: efbea2f9b8502f2521f2376f4aaf57902fa4e302
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498457"
---
# <a name="delete-data-from-azure-data-explorer"></a>從 Azure 資料總管刪除資料

Azure 資料資源管理員支援本文中描述的各種刪除方案。 

## <a name="delete-data-using-the-retention-policy"></a>使用保留原則移除資料

Azure 資料資源管理員根據[保留策略](kusto/management/retentionpolicy.md)自動刪除數據。 此方法是刪除數據的最有效和無憂無慮的方法。 在資料庫或表級別設置保留策略。

假設有一個保留期設為 90 天的資料庫或資料表。 如果只需要 60 天的數據,請刪除較舊的數據,如下所示:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>以刪除延伸區移除資料

[範圍(數據分片)](kusto/management/extents-overview.md)是存儲數據的內部結構。 每個範圍可以容納多達數百萬條記錄。 可以使用[放置範圍(s) 命令](kusto/management/extents-commands.md#drop-extents)單獨刪除擴展盤區或作為組刪除。 

### <a name="examples"></a>範例

您可以刪除表中的所有行,也可以僅刪除特定範圍。

* 刪除表中的所有行:

    ```kusto
    .drop extents from TestTable
    ```

* 刪除特定範圍:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>使用清除刪除單列

[資料清除](kusto/concepts/data-purge.md)可用於刪除個人行。 刪除不是即時的,需要大量的系統資源。 因此,僅建議針對合規性方案。  

