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
ms.openlocfilehash: 333c4d59a7ed62fd031ab52019c10abd821fd858
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226749"
---
# <a name="getschema-operator"></a>getschema 運算子 

產生代表輸入之表格式架構的資料表。

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**語法**

*T* `| ``getschema`

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|BytesDelivered|4|System.Int64|long
