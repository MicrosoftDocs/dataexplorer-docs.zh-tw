---
title: 。 alter table docstring-Azure 資料總管
description: 本文說明 `.alter table docstring` Azure 資料總管中的命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 65b71ab15763506683c461f04975d22d396f6405
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512532"
---
# <a name="alter-table-docstring"></a>.alter 資料表 docstring

改變現有資料表的 DocString 值。

`.alter``table` *TableName* `docstring` *檔*

> [!NOTE]
> * 需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 原先建立資料表的[資料庫使用者](../management/access-control/role-based-authorization.md)可以修改它
> * 如果資料表不存在，則會傳回錯誤。 若要建立新的資料表，請參閱[. create table](create-table-command.md)

**範例** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
