---
title: 表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: fd47a8f59c716148dfc5f89ac1d4c7aca45a8e9c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509277"
---
# <a name="tables"></a>資料表

資料表是可保存資料的具名實體。 表具有一組有序的[列](./columns.md),以及零行或更多行的數據,每行都包含表的每個列的一個數據值。 表中行的順序未知,通常不會影響查詢,但某些表格運算符(如[頂部運算符](../topoperator.md))本質上是不確定的。

表佔用與[儲存函數](./stored-functions.md)相同的命名空間,因此,如果存儲的函數和表具有相同的名稱,則將選擇存儲的函數。

**注意事項**  

* 表名稱區分大小寫。
* 表名稱遵循[實體名稱](./entity-names.md)的規則。
* 每個資料庫表的最大限制為 10,000。


有關如何創建和管理表的詳細資訊,請參閱[管理表](../../management/tables.md)

## <a name="table-references"></a>表參考

引用表的最簡單方法是使用表的名稱。 這可以針對上下文中資料庫中的所有表執行此操作。 因此,例如,以下查詢對目前資料庫表的記錄`StormEvents`進行計數:

```kusto
StormEvents
| count
```

請注意,編寫上述查詢的等效方法是通過轉義表名稱:

```kusto
["StormEvents"]
| count
```

表還可以通過顯式記錄它們位於的資料庫(或資料庫和群集)來引用,從而允許一個表編寫組合來自多個資料庫和群集的數據的查詢。 例如,只要調用方有權訪問目標資料庫,以下查詢將適用於上下文中的任何資料庫:

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

也可以使用[表() 特殊函數](../tablefunction.md)引用表,只要該函數的參數計算為常量。 例如：

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```