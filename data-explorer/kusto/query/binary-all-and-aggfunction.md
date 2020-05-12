---
title: binary_all_and （）（彙總函式）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 binary_all_and （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9f0e1665010885a64e6d97151b074d3a03df829b
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227565"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and （）（彙總函式）

`AND`針對每個摘要群組使用二元運算來累積值（如果沒有群組則執行摘要作業則為 total）。

* 只能在[匯總](summarizeoperator.md)的內容中使用

**語法**

總結 `binary_all_and(` *Expr*`)`

**引數**

* *Expr*： long 數位。

**傳回**

傳回一個值，其使用 `AND` 每個摘要群組之記錄的二進位運算來匯總（如果沒有群組，則會匯總）。

**範例**

使用二元運算來產生「咖啡館-食物」 `AND` ：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0xFFFFFFFF, 
  0xFFFFF00F,
  0xCFFFFFFD,
  0xFAFEFFFF,
]
| summarize result = toupper(tohex(binary_all_and(num)))
```

|result|
|---|
|CAFEF00D|
