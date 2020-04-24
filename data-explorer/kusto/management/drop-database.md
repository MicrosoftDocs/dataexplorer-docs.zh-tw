---
title: .刪除資料庫漂亮名稱 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 .drop 資料庫名稱。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a8a74b871ddcf420f39bb45ebdf3bce36f026105
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521177"
---
# <a name="drop-database-prettyname"></a>.drop 資料庫名稱漂亮

刪除資料庫的漂亮(友好)名稱。
需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)。

**語法**

`.drop``database`*資料庫名稱*`prettyname`

**傳回輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName |String |資料庫名稱
|漂亮名稱 |String |資料庫名稱(移除操作後為空)