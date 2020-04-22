---
title: .drop 表和 .drop 表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .drop 表和 .drop 表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 5f3a488aba5a6785ceb6ad4a093c520ec0509e5e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744747"
---
# <a name="drop-table-and-drop-tables"></a>.下拉錶和 .drop 表

從資料庫中刪除表(或多個表)。

需要[表員權限](../management/access-control/role-based-authorization.md)。

> [!NOTE]
> 該`.drop``table`命令僅軟刪除數據(即數據無法查詢,但仍可從持久存儲恢復)。 根據在將數據引入表中時有效的`recoverability`[保留策略](../management/retentionpolicy.md)中的屬性,硬刪除基礎存儲專案。

**語法**

`.drop``table`*表名*`ifexists`|

`.drop``tables` (*表格名稱 1,**表格名稱 2*,..)[如果存在]

> [!NOTE]
> 如果`ifexists`指定,則如果存在不存在的表,該命令不會失敗。

**範例**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**傳回**

此命令返回資料庫中其餘表的清單。 

| 輸出參數 | 類型   | 描述                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | 資料表的名稱。                  |
| DatabaseName     | String | 表所屬的資料庫。 |