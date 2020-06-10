---
title: 。 alter table-Azure 資料總管
description: 本文描述. alter table 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: 29f0c65a635b6e4fe6ffe3288cc1dcdde702fc8a
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84665022"
---
# <a name="alter-table"></a>.alter 資料表
 
`.alter table`命令：
* 保護「保留」資料行中的資料
* 重新排序資料表資料行
* 將新的資料行架構、 `docstring` 和資料夾設定為現有的資料表，並覆寫現有的資料行架構、 `docstring` 和資料夾
* 必須在範圍資料表名稱的特定資料庫內容中執行
* 需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)

> [!WARNING]
> `.alter`不正確地使用命令可能會導致資料遺失。

> [!TIP]
> `.alter`有一個對應的，也就是 `.alter-merge` 具有類似功能的 table 命令。 如需詳細資訊，請參閱[. alter-merge table](../management/alter-merge-table-command.md)

**語法**

`.alter``table` *TableName* （*columnName*：*columnType*，...） [ `with` `(` [ `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾*集] `)` ]


 * 資料表的資料行與指定的順序會完全相同。
 指定資料表資料行：
 * 如果未在命令中指定現有的資料行，則會卸載它們，而其中的資料將會遺失，例如使用 `.drop column` 命令。
 * 當您更改資料表時，不支援改變數據行類型。 請改用[. alter column](alter-column.md)命令。

> [!TIP]
> `.show table [TableName] cslschema`您可以使用來取得現有的資料行架構，然後再加以修改。


命令會如何影響資料？
* 命令不會實際修改現有的資料。 已移除資料行中的資料會被忽略。 新資料行中的資料會假設為 null。
* 視叢集的設定方式而定，資料內嵌可能會修改資料表的資料行架構，即使沒有使用者互動也一樣。 當您對資料表的資料行架構進行變更時，請確定內嵌不會加入所需的資料行，而該命令將會移除。

> [!WARNING]
> 在修改資料表資料行架構的資料表中，以及與命令平行發生的資料內嵌程式 `.alter table` ，可能會與資料表資料行的循序執行無關。 此外，也會有將資料內嵌到錯誤資料行的風險。 請在命令期間停止內嵌，或確定這類內嵌作業一律使用對應物件，以避免這些問題。

**範例**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
