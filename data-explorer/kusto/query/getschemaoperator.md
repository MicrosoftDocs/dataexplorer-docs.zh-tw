---
title: getschema 操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的 getschema 操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6960a737b0a71a6b921a6a58750e48f5c3fb9da0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247370"
---
# <a name="getschema-operator"></a>getschema 運算子 

產生表示輸入之表格式架構的資料表。

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
|時間戳記|0|System.DateTime|Datetime|
|語言|1|System.String|字串|
|頁面|2|System.String|字串|
|檢視|3|System.Int64|long
|BytesDelivered|4|System.Int64|long
