---
title: datatable 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 datatable 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2a5881eacd0702720b7ea4b9a3237731a56a5180
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265029"
---
# <a name="datatable-operator"></a>datatable 運算子

傳回資料表，其架構和值在查詢本身中定義。

> [!NOTE]
> 這個運算子沒有管線輸入。

**語法**

`datatable``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *scalarvalue 具有*[ `,` *scalarvalue 具有*...]`]`

**引數**

::: zone pivot="azuredataexplorer"

* *ColumnName*、 *ColumnType*：這些引數會定義資料表的架構。 引數會使用與定義資料表時所使用的相同語法。
  如需詳細資訊，請參閱[create table](../management/create-table-command.md)）。
* *Scalarvalue 具有*：要插入資料表中的常數純量值。 值的數目必須是資料表中資料行的整數倍數。 第*n*個值必須具有對應至資料行*n*  %  *NumColumns*的類型。

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*、 *ColumnType*：這些引數會定義資料表的架構。
* *Scalarvalue 具有*：要插入資料表中的常數純量值。 值的數目必須是資料表中資料行的整數倍數。 第*n*個值必須具有對應至資料行*n*  %  *NumColumns*的類型。

::: zone-end

**傳回**

這個運算子會傳回給定架構和資料的資料表。

**範例**

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
