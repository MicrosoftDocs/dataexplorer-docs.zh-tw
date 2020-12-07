---
title: 。 alter table-Azure 資料總管
description: 本文描述 alter table 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: ccefca3d3cbf1f97661fead54bbc3cfaf207ca1a
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321670"
---
# <a name="alter-table"></a>.alter 資料表
 
`.alter table`命令：
* 保護「保留」資料行中的資料
* 重新排序資料表資料行
* 將新的資料行架構、 `docstring` 和資料夾設定為現有的資料表，並覆寫現有的資料行架構、 `docstring` 和資料夾
* 必須在範圍為數據表名稱的特定資料庫內容中執行
* 需要 [資料表管理員許可權](../management/access-control/role-based-authorization.md)

> [!WARNING]
> 不當使用 `.alter` 命令可能會導致資料遺失。

> [!TIP]
> `.alter`具有對應的，也就是 `.alter-merge` 具有類似功能的資料表命令。 如需詳細資訊，請參閱 [`.alter-merge table`](../management/alter-merge-table-command.md)

**語法**

`.alter``table` *TableName* (*columnName*：*columnType*，... ) [ `with` `(` `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾資料夾*] `)` ]


 * 資料表將會依照指定的相同順序，擁有完全相同的資料行。
 指定資料表資料行：
 * 如果未在命令中指定現有的資料行，則會卸載這些資料行，而且這些資料行中的資料將會遺失，例如使用 `.drop column` 命令。
 * 當您改變數據表時，不支援改變數據行類型。 請改為使用 [`.alter column`](alter-column.md) 命令。

> [!TIP]
> `.show table [TableName] cslschema`您可以使用來取得現有的資料行架構，然後再加以修改。


此命令會如何影響資料？
* 命令不會實際修改現有的資料。 已移除資料行中的資料會被忽略。 新資料行中的資料會假設為 null。
* 視叢集的設定方式而定，資料內嵌可能會修改資料表的資料行架構（即使沒有使用者互動）。 當您對資料表的資料行架構進行變更時，請確定內嵌不會加入所需的資料行，然後命令將會移除該資料行。

> [!WARNING]
> 將資料內嵌在修改資料表資料行架構的資料表中，以及與命令平行發生的程式 `.alter table` ，可能會與資料表資料行的順序無關。 此外，也有可能會將資料內嵌至錯誤的資料行。 請在命令期間停止內嵌，或確定這類內嵌作業一律使用對應物件，以避免這些問題。

**範例**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
