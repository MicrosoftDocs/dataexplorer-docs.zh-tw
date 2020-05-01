---
title: 。顯示資料表-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中顯示資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3da4f705d3182c52d06c7767a12d9be15a219e5c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618321"
---
# <a name="show-tables"></a>。顯示資料表

傳回集合，其中包含指定的資料表或資料庫中的所有資料表。

需要[資料庫檢視器許可權](../management/access-control/role-based-authorization.md)。

```kusto
.show tables
.show tables (T1, ..., Tn)
```

**輸出**

|輸出參數 |類型 |描述
|---|---|---
|TableName  |String |資料表的名稱。
|DatabaseName  |String |資料表所屬的資料庫。
|資料夾 |String |資料表的資料夾。
|DocString |String |記錄資料表的字串。

**輸出範例**

|資料表名稱 |資料庫名稱 |資料夾 | DocString
|---|---|---|---
|Table1 |DB1 |記錄 |包含服務記錄檔
|Table2 |DB1 | 報告 |
|Table3 |DB1 | | 擴充資訊 |
|Table4 |DB2 | 計量| 包含服務效能資訊
