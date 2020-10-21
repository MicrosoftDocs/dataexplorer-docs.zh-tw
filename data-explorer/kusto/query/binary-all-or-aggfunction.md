---
title: 'binary_all_or ( # A1 (彙總函式) -Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) binary_all_or。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bff5d631493b9431d6e1a5fa88d3dd419147df96
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247963"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or ( # A1 (彙總函式) 

使用 `OR` 每個摘要群組的二進位運算來累積值 (或總計，以在沒有群組) 的情況下完成摘要。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `binary_all_or(` *運算式*`)`

## <a name="arguments"></a>引數

* *Expr*： long 數位。

## <a name="returns"></a>傳回

傳回值，這個值會在 `OR` 每個摘要群組 (或總計的記錄上，使用二進位運算來匯總，如果未分組) ，則會進行匯總。

## <a name="example"></a>範例

使用二元運算來產生「咖啡館」-食物 `OR` ：

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
