---
title: .alter 資料庫名稱 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 .alter 資料庫在 Azure 數據資源管理員中的名稱。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1b362b547dc980676108ec169a0abb97f189375b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522588"
---
# <a name="alter-database-prettyname"></a>.alter 資料庫名稱

更改資料庫的漂亮(友好)名稱。

需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)。

**語法**

`.alter``database`*DatabaseName*資料庫`prettyname`名稱`'`*資料庫名稱*`'`

**傳回輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName |String |資料庫的名稱。
|漂亮名稱 |String |資料庫的漂亮名稱。

