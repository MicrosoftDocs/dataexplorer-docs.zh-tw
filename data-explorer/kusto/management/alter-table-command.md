---
title: 。 alter table 和 alter-merge table-Azure 資料總管 |Microsoft Docs
description: 本文描述 Azure 資料總管中的 alter table 和 alter-merge 資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 18b0502754e95ca56d6a7f6946b9330bd42b174a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616434"
---
# <a name="alter-table-and-alter-merge-table"></a>。 alter table 和 alter-merge 資料表

`.alter table`命令會將新的資料行架構、docstring 和資料夾設定為現有的資料表，並覆寫現有的資料行架構、docstring 和資料夾。 系統會保留命令所「保留」之現有資料行中的資料（例如，可以使用此命令來重新排列資料表的資料行）。

命令`.alter-merge table`會將新的資料行 [docstring] 和 [資料夾] 加入至現有的資料表。
保留現有資料行中的資料。

這兩個命令都必須在範圍資料表名稱的特定資料庫內容中執行。

需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

**語法**

`.alter``table` *TableName* （*columnName*：*columnType*，...） [`with` `(`[`docstring` `=` *FolderName* *Documentation* `,`檔] [ `folder`資料夾集`)`]] `=`

`.alter-merge``table` *TableName* （*columnName*：*columnType*，...） [`with` `(`[`docstring` `=` *FolderName* *Documentation* `,`檔] [ `folder`資料夾集`)`]] `=`

指定資料表成功完成後應具有的資料行。 

> [!WARNING]
> 不正確`.alter`地使用命令可能會導致資料遺失。
> 仔細閱讀和`.alter-merge`以下之間`.alter`的差異。

`.alter-merge`:

 * 不存在且您指定的資料行會加入至現有架構的結尾。
 * 如果傳遞的架構不包含某些資料表資料行，則不會將它們刪除。
 * 如果您指定了具有不同類型的現有資料行，此命令將會失敗。

`.alter`僅限（ `.alter-merge`不）：

 * 資料表的資料行與指定的順序會完全相同。
 * 未在命令中指定的現有資料行將會卸載（如中`.drop column`所示），而且其中的資料會遺失。
 * 更改資料表時，不支援變更資料行類型。 請改用[. alter column](alter-column.md)命令。

> [!TIP] 
> 您`.show table [TableName] cslschema`可以使用來取得現有的資料行架構，然後再加以修改。 

以下適用于這兩個命令：

1. 這些命令不會實際修改現有的資料。 已移除資料行中的資料會被忽略。 新資料行中的資料會假設為 null。
1. 視叢集的設定方式而定，資料內嵌可能會修改資料表的資料行架構，即使沒有使用者互動也一樣。 因此，在對資料表的資料行架構進行變更時，請確定內嵌不會加入所需的資料行，而該命令將會移除。

> [!WARNING]
> 在修改資料表的資料行架構的資料表中，會以與`.alter table`命令平行發生的方式來執行資料內嵌程式，可能會與資料表資料行的順序無關。 此外，也會有將資料內嵌到錯誤資料行的風險。 若要避免此情況，請在這類命令中停止內嵌，或確定這類內嵌作業一律使用對應物件。

**範例**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```