---
title: column_ifexists() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 column_ifexists()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4f7d4a2114aa2b9b3bb8ae3e951306a3ffb725e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517165"
---
# <a name="column_ifexists"></a>column_ifexists()

將列名作為字串和預設值。 如果列存在,則返回對列的引用,否則返回預設值。

**語法**

`column_ifexists(`*欄位*`, `*名稱 預設值*)

**引數**

* *欄位*: 欄的名稱
* *預設值*:如果列在函數使用的上下文中不存在,則要使用的值。
                  此值可以是任何標量表達式(例如,對另一列的引用)。

**傳回**

如果*列名稱*存在,則它引用的列。 否則 ─*預設值*。

**範例**

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```