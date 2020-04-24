---
title: .重新命名表和 .重新命名表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .重新命名表和 .重新命名表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: db3e38c562573f52df70979b71fe72d8947158bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520480"
---
# <a name="rename-table-and-rename-tables"></a>.重新命名表和 .重新命名表

更改現有表的名稱。

該`.rename tables`命令將資料庫中許多表的名稱更改為單個事務。

需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)。

**語法**

`.rename``table`*舊名稱*`to`*新名稱*

`.rename``tables`*新名稱* = *舊名稱*`ifexists`[`,` ] [ ]

> [!NOTE]
> * *"舊名稱'* 是現有表的名稱。 如果*OldName*不命名現有表,則引發錯誤,整個命令失敗(無效),除非`ifexists`指定(在這種情況下,重新命名命令的這一部分將被忽略)。
> * *NewName*是以前稱為*OldName*的現有表的新名稱。
> * 如果`ifexists`指定,它將修改命令的行為以忽略重新命名不存在的表的部分。

**備註**

此命令僅在作用域中的資料庫表上操作。
無法用群集或資料庫名稱限定表名稱。

此命令不會創建新錶,也不會刪除現有表。
命令描述的轉換必須使資料庫中的表數不更改。

該命令**支援**交換表名稱或更複雜的排列,只要它們符合上述規則。 例如,將數據引入多個暫存表中,然後將其與單個事務中的現有表交換。

**範例**

想像資料庫,`A`具有以下表格 : `B` `C``A_TEMP`、 與 。
`A`以下命令將交換`A_TEMP`和 (`A_TEMP`以便現在`A`調用 表 ,`B`然後`NEWB`相反),`C`重新命名 為 ,並保持" 

```
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

以下指令序列:
1. 建立新的暫存表
1. 將現有表或現有表取代為新表

```
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```