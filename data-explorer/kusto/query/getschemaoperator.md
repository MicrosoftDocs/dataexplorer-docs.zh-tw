---
title: 取得架構運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 getschema 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2faeee575f1af72cfad808253853ae96aba7a28f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514360"
---
# <a name="getschema-operator"></a>getschema 運算子 

生成表示輸入的表格架構的表。

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**語法**

*T* `| ``getschema`

**範例**

```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|ColumnType|
|---|---|---|---|
|時間戳記|0|System.DateTime|Datetime|
|Language|1|System.String|字串|
|頁面|2|System.String|字串|
|檢視|3|System.Int64|long
|位元組傳遞|4|System.Int64|long