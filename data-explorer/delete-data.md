---
title: 從 Azure 資料總管刪除資料
description: 本文說明 Azure 資料總管中的刪除案例，包括清除、捨棄範圍和以保留為基礎的刪除。
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: fb9cdfbef5b4d2aa7c7b98fdc58d2ec7fdccbd0c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874234"
---
# <a name="delete-data-from-azure-data-explorer"></a>從 Azure 資料總管刪除資料

Azure 資料總管支援本文中所述的各種不同刪除案例。 

## <a name="delete-data-using-the-retention-policy"></a>使用保留原則刪除資料

Azure 資料總管會根據 [保留原則](kusto/management/retentionpolicy.md)自動刪除資料。 這種方法是刪除資料最有效率且不麻煩的方法。 在資料庫或資料表層級設定保留原則。

假設有一個保留期設為 90 天的資料庫或資料表。 如果只需要60天的資料，請刪除較舊的資料，如下所示：

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>藉由捨棄範圍來刪除資料

[範圍 (資料分區) ](kusto/management/extents-overview.md) 是儲存資料的內部結構。 每個範圍最多可保存數百萬筆記錄。 您可以使用 [drop 片區 (s) 命令](kusto/management/extents-commands.md#drop-extents)，個別刪除範圍或群組。 

### <a name="examples"></a>範例

您可以刪除資料表中的所有資料列，或只刪除特定的範圍。

* 刪除資料表中的所有資料列：

    ```kusto
    .drop extents from TestTable
    ```

* 刪除特定範圍：

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>使用清除來刪除個別的資料列

您可以使用[資料清除](kusto/concepts/data-purge.md)來刪除個人資料列。 刪除不是即時的，且需要大量的系統資源。 因此，它只會針對合規性案例提供建議。  

