---
title: 'tdigest_merge ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 tdigest_merge。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 4b7c143d29b8a2f446f4929098e9fac4e6166796
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250618"
---
# <a name="tdigest_merge"></a>tdigest_merge()

合併 `tdigest` 結果 (純量版本) 的純量版本 [`tdigest_merge()`](tdigest-merge-aggfunction.md) 。

深入瞭解基礎演算法 (T 摘要) 和 [此處](percentiles-aggfunction.md#estimation-error-in-percentiles)的預估錯誤。

## <a name="syntax"></a>語法

`merge_tdigests(`*運算式 1* `,`*運算式 2*`, ...)`

`tdigest_merge(`*運算式 1* `,`*運算式 2* `, ...)`-別名。

## <a name="arguments"></a>引數

* 包含 `tdigest` 要合併之值的資料行。

## <a name="returns"></a>傳回

將資料行 `*Expr1*` （ `*Expr2*` ，...）合併 `*ExprN*` 到一個資料行的 `tdigest` 結果。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize tdigestX = tdigest(x), tdigestY = tdigest(y)
| project merged = tdigest_merge(tdigestX, tdigestY)
| project percentile_tdigest(merged, 100, typeof(long))
```

|percentile_tdigest_merged|
|---|
|20|
