---
title: .顯示表架構 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .show 表架構。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 9223baa0242cc936b25a7d3293d102cb3966ed53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519766"
---
# <a name="show-table-schema"></a>.顯示表架構

獲取要在創建/更改命令和其他表元數據中使用的架構。

需要[資料庫使用者權限](../management/access-control/role-based-authorization.md)。

```
.show table TableName cslschema 
```
| 輸出參數 | 類型   | 描述                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | 資料表的名稱。                                    |
| 結構描述           | String | 表架構,如應用程式於表建立/變更 |
| DatabaseName     | String | 表所屬的資料庫                   |
| 資料夾           | String | 表的資料夾                                            |
| 文件字串        | String | 表的文件串                                         |


## <a name="show-table-schema-as-json"></a>.將表架構顯示為 JSON

獲取 JSON 格式的架構和其他表元數據。

需要[資料庫使用者權限](../management/access-control/role-based-authorization.md)。

```
.show table TableName schema as JSON
```

| 輸出參數 | 類型   | 描述                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | 資料表的名稱                   |
| 結構描述           | String | JSON 格式的表架構         |
| DatabaseName     | String | 表所屬的資料庫 |
| 資料夾           | String | 表的資料夾                          |
| 文件字串        | String | 表的文件串                       |