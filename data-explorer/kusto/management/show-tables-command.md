---
title: .顯示表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .show 表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a8faf307a241d1ba0f73436d9503a56c9078e471
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519630"
---
# <a name="show-tables"></a>.顯示表

返回包含指定表或資料庫中所有表的集。

需要[資料庫檢視器權限](../management/access-control/role-based-authorization.md)。

```
.show tables
.show tables (T1, ..., Tn)
```

**輸出**

|輸出參數 |類型 |描述
|---|---|---
|TableName  |String |資料表的名稱。
|DatabaseName  |String |表所屬的資料庫。
|資料夾 |String |表的資料夾。
|文件字串 |String |記錄表的字串。

**輸出範例**

|資料表名稱 |資料庫名稱 |資料夾 | 文件字串
|---|---|---|---
|Table1 |DB1 |記錄 |包含服務紀錄
|Table2 |DB1 | 報告 |
|Table3 |DB1 | | 擴充資訊 |
|表4 |DB2 | 計量| 包含服務效能資訊