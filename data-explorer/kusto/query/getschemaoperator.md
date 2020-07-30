---
title: getschema 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 getschema 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4fe19148ef8f410f04dc68f435734a2c2c425cca
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347675"
---
# <a name="getschema-operator"></a>getschema 運算子 

產生代表輸入之表格式架構的資料表。

```kusto
T | summarize MyCount=count() by Country | getschema 
```

## <a name="syntax"></a>語法

*T* `| ``getschema`

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|ColumnType|
|---|---|---|---|
|Timestamp|0|System.DateTime|Datetime|
|Language|1|System.String|字串|
|頁面|2|System.String|字串|
|檢視|3|System.Int64|long
|BytesDelivered|4|System.Int64|long
