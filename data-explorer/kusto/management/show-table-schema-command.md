---
title: 。顯示資料表架構-Azure 資料總管 |Microsoft Docs
description: 本文說明。在 Azure 資料總管中顯示資料表架構。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 899e54b46dd231db0bf1272c0eb1933dad474a47
ms.sourcegitcommit: 2ebd83369f247cf6dd91709f26e4ecd873489eaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83555010"
---
# <a name="show-table-schema"></a>.show 資料表結構描述

取得要在 create/alter 命令和其他資料表中繼資料中使用的架構。

需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

```kusto
.show table TableName cslschema 
```

| 輸出參數 | 類型   | 說明                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | 資料表的名稱。                                    |
| 結構描述           | String | 資料表架構 as 應該用於資料表建立/改變 |
| DatabaseName     | String | 資料表所屬的資料庫                   |
| 資料夾           | String | 資料表的資料夾                                            |
| DocString        | String | 資料表的 docstring                                         |


## <a name="show-table-schema-as-json"></a>。以 JSON 形式顯示資料表架構

取得 JSON 格式的架構和其他資料表中繼資料。

需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

```kusto
.show table TableName schema as json
```

| 輸出參數 | 類型   | 說明                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | 資料表的名稱                   |
| 結構描述           | String | JSON 格式的資料表架構         |
| DatabaseName     | String | 資料表所屬的資料庫 |
| 資料夾           | String | 資料表的資料夾                          |
| DocString        | String | 資料表的 docstring                       |
