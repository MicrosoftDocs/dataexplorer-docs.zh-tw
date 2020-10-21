---
title: datatable 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 datatable 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6a5f12c947d62b7ec13ab6d9d6a4564881de08a2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247851"
---
# <a name="datatable-operator"></a>datatable 運算子

傳回資料表，其架構和值是在查詢本身中定義。

> [!NOTE]
> 這個運算子沒有管線輸入。

## <a name="syntax"></a>語法

`datatable``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *ScalarValue* [ `,` *ScalarValue* ...]`]`

## <a name="arguments"></a>引數

::: zone pivot="azuredataexplorer"

* *ColumnName*、 *ColumnType*：這些引數會定義資料表的架構。 引數所使用的語法與定義資料表時所使用的語法相同。
  如需詳細資訊，請參閱 [。建立資料表](../management/create-table-command.md)) 。
* *ScalarValue*：要插入資料表中的常數純量值。 值的數目必須是資料表中資料行的整數倍數。 *N*' th 值必須有對應到資料行*n*  %  *NumColumns*的類型。

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*、 *ColumnType*：這些引數會定義資料表的架構。
* *ScalarValue*：要插入資料表中的常數純量值。 值的數目必須是資料表中資料行的整數倍數。 *N*' th 值必須有對應到資料行*n*  %  *NumColumns*的類型。

::: zone-end

## <a name="returns"></a>傳回

這個運算子會傳回指定架構和資料的資料表。

## <a name="example"></a>範例

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
