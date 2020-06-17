---
title: 資料表-Azure 資料總管
description: 本文說明 Azure 資料總管中的資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: fa97777da8173034098037f1aceec385a4c206de
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818609"
---
# <a name="tables"></a>資料表

資料表是可保存資料的具名實體。 資料表具有一組已排序的資料行，以及零或多個資料[列](./columns.md)。 每個資料列都會針對資料表的每個資料行保存一個資料值。 資料表中的資料列順序是未知的，而且一般不會影響查詢，除了原本無法確定的某些表格式運算子（例如[top 運算子](../topoperator.md)）。

資料表佔用與[預存函數](./stored-functions.md)相同的命名空間。
如果預存函數和資料表都有相同的名稱，則會選擇儲存的函式。

**注意事項**  

* 資料表名稱會區分大小寫。
* 資料表名稱會遵循[機構名稱](./entity-names.md)的規則。
* 每個資料庫的資料表上限為10000。


如需如何建立和管理資料表的詳細資訊，請參閱[管理資料表](../../management/tables.md)

## <a name="table-references"></a>資料表參考

參考資料表最簡單的方式是使用其名稱。 此參考可針對資料庫中所有位於內容中的資料表進行。 例如，下列查詢會計算目前資料庫資料表的記錄 `StormEvents` ：

```kusto
StormEvents
| count
```

撰寫上述查詢的對等方式是藉由將資料表名稱進行轉義：

```kusto
["StormEvents"]
| count
```

資料表也可以藉由明確記出它們所在的資料庫（或資料庫和叢集）來參考。 接著，您可以撰寫結合多個資料庫和叢集之資料的查詢。 例如，只要呼叫者具有目標資料庫的存取權，下列查詢就會使用內容中的任何資料庫：

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

您也可以使用[table （）特殊函數](../tablefunction.md)來參考資料表，只要該函數的引數評估為常數即可。 例如：

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```

> [!NOTE]
> 使用 `table()` 特殊函數來明確指定資料表資料範圍。 例如，您可以使用此函式來限制處理位於熱快取中的資料表資料。
