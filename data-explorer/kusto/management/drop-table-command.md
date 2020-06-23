---
title: 。卸載資料表和 drop 資料表-Azure 資料總管
description: 本文將說明如何在 Azure 資料總管中卸載 table 和 drop 資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 3e1eb57741302d34664f6cd8f256612a6e70bdd1
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264482"
---
# <a name="drop-table-and-drop-tables"></a>。 drop table 和 drop tables

從資料庫中移除資料表或多個資料表。

需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

> [!NOTE]
> 此 `.drop` `table` 命令只會虛刪除資料。 也就是說，無法查詢資料，但仍可從持續性儲存體復原。 基礎儲存體成品會根據在 `recoverability` 資料內嵌至資料表時生效的[保留原則](../management/retentionpolicy.md)中的屬性進行實刪除。

**語法**

`.drop``table` *TableName* [ `ifexists` ]

`.drop``tables`（*TableName1*， *TableName2*,..） [ifexists]

> [!NOTE]
> 如果 `ifexists` 指定了，當有不存在的資料表時，此命令就不會失敗。

**範例**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**傳回**

此命令會傳回資料庫中其餘資料表的清單。

| 輸出參數 | 類型   | 描述                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | 資料表的名稱。                  |
| DatabaseName     | String | 資料表所屬的資料庫。 |
