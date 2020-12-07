---
title: alter-merge table-Azure 資料總管
description: 本文描述 alter merge table 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: 1a58d44e7884fb198f04a9f12a71c77cf164331b
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321687"
---
# <a name="alter-merge-table"></a>.alter-merge table
 
`.alter-merge table`命令：

* 保護現有資料行中的資料
* 將新的資料行、 `docstring` 和資料夾新增至現有的資料表
* 必須在範圍為數據表名稱的特定資料庫內容中執行
* 需要 [資料表管理員許可權](../management/access-control/role-based-authorization.md)

> [!WARNING]
> 不當使用 `.alter-merge` 命令可能會導致資料遺失。

> [!TIP]
> `.alter-merge`具有對應的，也就是 `.alter` 具有類似功能的資料表命令。 如需詳細資訊，請參閱 [`.alter table`](../management/alter-table-command.md)

**語法**

`.alter-merge``table` *TableName* (*columnName*：*columnType*，... ) [ `with` `(` `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾資料夾*] `)` ]

指定資料表資料行：
 * 不存在的資料行以及您指定的資料行，會加入至現有架構的結尾。
 * 如果傳遞的架構未包含部分資料表資料行，則不會刪除資料行。
 * 如果您指定具有不同類型的現有資料行，此命令將會失敗。

> [!TIP]
> `.show table [TableName] cslschema`您可以使用來取得現有的資料行架構，然後再加以修改。

此命令會如何影響資料？
* 命令不會實際修改現有的資料。 已移除資料行中的資料會被忽略。 新資料行中的資料會假設為 null。
* 視叢集的設定方式而定，資料內嵌可能會修改資料表的資料行架構（即使沒有使用者互動）。 當您對資料表的資料行架構進行變更時，請確定內嵌不會加入所需的資料行，然後命令將會移除該資料行。

**範例**

```kusto
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
 
