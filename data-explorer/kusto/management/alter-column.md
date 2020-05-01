---
title: 。 alter column-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 alter column。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: ecf0fa09438f8df5792d8826150d58f06360cace
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617862"
---
# <a name="alter-column"></a>.alter 資料行

改變現有資料表資料行的資料類型。

> [!WARNING]
> 改變數據行的資料類型時，未來的查詢將忽略該資料行中，不是新資料類型的任何預先存在的資料，並將其取代為[null 值](../query/scalar-data-types/null-values.md)。 使用`alter column`之後，即使透過使用另一個命令將資料行類型變更回先前的值，也無法復原該資料。
> 請參閱[下文](#changing-column-type-without-data-loss)，以取得變更資料行類型的建議程式，而不會遺失資料。

**語法** 

`.alter``column` [*DatabaseName* `.`] *TableName* `.` *ColumnName* ColumnName `type` *ColumnNewType* ColumnNewType `=`
 
**範例** 

```kusto
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>變更資料行類型而不遺失資料

若要在保留歷程記錄資料時變更資料行類型，請建立一個新的、正確類型的資料表。

針對您想`T1`要在中變更資料行類型的每個資料表，執行下列步驟：

1. 建立具有正確`T1_prime`架構的資料表（正確的資料行類型）。
1. 使用來交換資料表[。 [重新命名資料表]](rename-table-command.md)命令可讓您交換資料表名稱。

```kusto
.rename tables T_prime=T1, T1=T_prime
```

當命令完成時，新`T1`的資料流程現在會正確輸入，並在中`T1_prime`提供歷程記錄資料。

在`T1_prime`資料離開保留期間之前，必須更改查詢`T1`以配合執行聯`T1_prime`集。