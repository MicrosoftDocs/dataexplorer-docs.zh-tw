---
title: 。 alter table docstring-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 alter table docstring。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 790cd9805be6dd4440ef2eb51c504044dc069b3c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617777"
---
# <a name="alter-table-docstring"></a>.alter 資料表 docstring

改變現有資料表的 DocString 值。

`.alter``table` *TableName* TableName `docstring` *檔*

> [!NOTE]
> * 需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 資料表的修改也可以用於原先建立資料表的[資料庫使用者](../management/access-control/role-based-authorization.md)。
> * 如果資料表不存在，則會傳回錯誤。 如需建立新的資料表，請參閱[. create table](create-table-command.md)

**範例** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```