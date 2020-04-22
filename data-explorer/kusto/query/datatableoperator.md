---
title: 資料表運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的數據表運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 182f8030e3263ee5bf6bee4ca7444b0d5596e7d6
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765391"
---
# <a name="datatable-operator"></a>datatable 運算子

返回其架構和值在查詢本身中定義的表。

請注意，此運算子沒有管線輸入。

**語法**

`datatable``(`*欄位*`:`*型態*`,`= ...]`)` *ScalarValue* ScalarValue = ScalarValue ...] `[` `,` *ScalarValue*`]`

**引數**

::: zone pivot="azuredataexplorer"

* *欄位*名稱,*欄型態*: 這些定義表的架構。 使用的語法與定義表時使用的語法完全相同(請參閱[.create 表](../management/create-table-command.md))。
* *ScalarValue*:要插入到表中的常量標量值。 值數必須是表中列的整數倍數 *,n'th*值必須具有對應於列*n* % *NumColumn*的類型。

::: zone-end

::: zone pivot="azuremonitor"

* *欄位*名稱,*欄型態*: 這些定義表的架構。
* *ScalarValue*:要插入到表中的常量標量值。 值數必須是表中列的整數倍數 *,n'th*值必須具有對應於列*n* % *NumColumn*的類型。

::: zone-end

**傳回**

此運算符返回給定架構和數據的數據表。

**範例**

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
