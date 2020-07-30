---
title: tdigest_merge （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 tdigest_merge （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: f85c2c45ff4e69ba59f2a13313c8c2ac494c56a6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340984"
---
# <a name="tdigest_merge"></a>tdigest_merge()

合併 `tdigest` 結果（匯總版本的純量版本 [`tdigest_merge()`](tdigest-merge-aggfunction.md) ）。

深入瞭解基礎演算法（T-摘要式）和[此處](percentiles-aggfunction.md#estimation-error-in-percentiles)的估計錯誤。

## <a name="syntax"></a>語法

`merge_tdigests(`*運算式 1* `,`*運算式 2*`, ...)`

`tdigest_merge(`*運算式 1* `,`*運算式 2* `, ...)`-別名。

## <a name="arguments"></a>引數

* 具有 `tdigest` 要合併之值的資料行。

## <a name="returns"></a>傳回

將資料行 `*Expr1*` （ `*Expr2*` ...）合併 `*ExprN*` 到其中的 `tdigest` 結果。

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
