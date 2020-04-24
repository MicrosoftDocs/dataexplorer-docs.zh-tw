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
ms.openlocfilehash: 5b3fa21b8b9012fe23a82afea77b1a3e24ae3c91
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108468"
---
# <a name="alter-table-docstring"></a>.alter 資料表 docstring

改變現有資料表的 DocString 值。

`.alter``table` *TableName* TableName `docstring` *檔*

> [!NOTE]
> * 需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 資料表的修改也可以用於原先建立資料表的[資料庫使用者](../management/access-control/role-based-authorization.md)。
> * 如果資料表不存在，則會傳回錯誤。 如需建立新的資料表，請參閱[. create table](create-table-command.md)

**範例** 

```
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```