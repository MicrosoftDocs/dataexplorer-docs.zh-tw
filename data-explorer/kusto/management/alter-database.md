---
title: 。 alter database prettyname-Azure 資料總管
description: 本文說明資料庫的「 `.alter` 非常名稱」命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fc445a7d85f52d672b92163cc358d8f3a741c68
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294503"
---
# <a name="alter-database-prettyname"></a>。 alter database prettyname

改變資料庫的相當（易記）名稱。

需要[DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

**語法**

`.alter``database` *DatabaseName* `prettyname` DatabaseName `'`*DatabasePrettyName*`'`

**傳回輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName |String |資料庫的名稱
|PrettyName |String |資料庫的非常名稱
