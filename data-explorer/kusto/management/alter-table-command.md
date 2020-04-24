---
title: .alter 表和 .alter-合併表 - Azure 數據資源管理器 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .alter 表和 .alter-merge 表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 516c5c7b85f7c310188fd11ae24e52cb23423143
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522367"
---
# <a name="alter-table-and-alter-merge-table"></a>.alter 表和 .alter-合併表

該`.alter table`命令將新的列架構、文檔字串和資料夾設置到現有表,覆蓋現有的列架構、文檔字串和資料夾。 命令"保留"的現有列中的數據將保留(因此,可以使用此命令重新排序表的列)。

這個`.alter-merge table`指令將新欄、文件字串和資料夾添加到現有表。
將保留現有列中的數據。

這兩個命令都必須在限定表名稱的特定資料庫的上下文中運行。

需要[表員權限](../management/access-control/role-based-authorization.md)。

**語法**

`.alter``table`*表名*(*欄位*:*欄型態*... ) 【`with` `(` `,``folder``=`*FolderName* `)` *Documentation*% 文件 [ 資料夾名稱 】 `docstring` `=`

`.alter-merge``table`*表名*(*欄位*:*欄型態*... ) 【`with` `(` `,``folder``=`*FolderName* `)` *Documentation*% 文件 [ 資料夾名稱 】 `docstring` `=`

指定表成功完成後應具有的列。 

> [!WARNING]
> 不正確使用`.alter`該命令可能會導致數據丟失。
> 仔細閱讀和`.alter-merge`下面的`.alter`差異 。

`.alter-merge`:

 * 不存在並指定哪些列將添加到現有架構的末尾。
 * 如果傳遞的架構不包含某些表列,則不會刪除它們。
 * 如果指定了具有不同類型的現有列,則該命令將失敗。

`.alter`只限制`.alter-merge`( 不 ):

 * 表將具有相同的列,按指定的順序相同。
 * 命令中未指定的現有列將被刪除(如中`.drop column`),其中的數據將丟失。
 * 更改表時不支援更改列類型。 而是使用[.alter 列](alter-column.md)命令。

> [!TIP] 
> 用於`.show table [TableName] cslschema`在更改現有列架構之前獲取它。 

以下兩個命令都適用:

1. 這些命令不會物理修改現有數據。 將忽略已刪除欄中的資料。 假定新列中的數據為空。
1. 根據群集的配置方式,數據引入可能會修改表的列架構,即使沒有使用者交互也是如此。 因此,在更改表的列架構時,請確保引入不會添加命令隨後將刪除所需的列。

> [!WARNING]
> 數據引入過程進入與`.alter table`命令並行修改表的列架構的表,可能會與表列的順序無關地執行。 還有一個風險是數據將被引入到錯誤的列中。 通過在此類命令期間停止引入,或者確保此類引入操作始終使用映射物件來防止這種情況。

**範例**

```
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```