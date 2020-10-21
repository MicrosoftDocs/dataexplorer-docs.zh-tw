---
title: 'binary_all_xor ( # A1 (彙總函式) -Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) binary_all_xor。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 7aa83f7c214c7bc45892ff1064a09dd84240b5f4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246720"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor ( # A1 (彙總函式) 

使用 `XOR` 每個摘要群組的二進位運算來累積值 (或總計，以在沒有群組) 的情況下完成摘要。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `binary_all_xor(` *運算式*`)`

## <a name="arguments"></a>引數

* *Expr*： long 數位。

## <a name="returns"></a>傳回

傳回值，這個值會在 `XOR` 每個摘要群組 (或總計的記錄上，使用二進位運算來匯總，如果未分組) ，則會進行匯總。

## <a name="example"></a>範例

使用二元運算來產生「咖啡館」-食物 `XOR` ：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x44404440,
  0x1E1E1E1E,
  0x90ABBA09,
  0x000B105A,
]
| summarize result = toupper(tohex(binary_all_xor(num)))
```

|result|
|---|
|CAFEF00D|
