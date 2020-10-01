---
title: 。清除資料表資料-Azure 資料總管
description: 本文說明 `.clear table data` Azure 資料總管中的命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vrozov
ms.service: data-explorer
ms.topic: reference
ms.date: 10/01/2020
ms.openlocfilehash: 513544b8fb373f7242bd36b250790801b39061b2
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91615066"
---
# <a name="clear-table-data"></a>。清除資料表資料

清除現有資料表的資料，包括串流內嵌資料。

`.clear``table` *TableName*`data` 

> [!NOTE]
> * 需要 [資料表管理員許可權](../management/access-control/role-based-authorization.md)

**範例** 

```kusto
.clear table LyricsAsTable data 
```
 
