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
ms.openlocfilehash: fc449cb6a376eba66457855173c6e992e6ca1dbc
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321653"
---
# <a name="alter-table-docstring"></a>.alter 資料表 docstring

改變現有資料表的 DocString 值。

`.alter``table` *TableName* `docstring` *檔*

> [!NOTE]
> * 需要 [資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 原先建立資料表的 [資料庫使用者](../management/access-control/role-based-authorization.md) 可以修改它
> * 如果資料表不存在，則會傳回錯誤。 若要建立新的資料表，請參閱 [`.create table`](create-table-command.md)

**範例** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
