---
title: binary_all_or （）（彙總函式）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 binary_all_or （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 35478b435814fe716f7130576c16714403490be6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349120"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or （）（彙總函式）

`OR`針對每個摘要群組使用二元運算來累積值（如果沒有群組則執行摘要作業則為 total）。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

總結 `binary_all_or(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*： long 數位。

## <a name="returns"></a>傳回

傳回一個值，其使用 `OR` 每個摘要群組之記錄的二進位運算來匯總（如果沒有群組，則會匯總）。

## <a name="example"></a>範例

使用二元運算來產生「咖啡館-食物」 `OR` ：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x88888008,
  0x42000000,
  0x00767000,
  0x00000005, 
]
| summarize result = toupper(tohex(binary_all_or(num)))
```

|result|
|---|
|CAFEF00D|
