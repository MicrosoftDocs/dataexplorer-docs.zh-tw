---
title: 'hll_merge ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 hll_merge。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 6bf364106bef8fbe626f96c22502dae748884180
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252967"
---
# <a name="hll_merge"></a>hll_merge()

合併 `hll` 結果 (純量版本) 的純量版本 [`hll_merge()`](hll-merge-aggfunction.md) 。

深入瞭解 [基礎演算法 (*H*Yper*l*og*l*og) 和估計精確度](dcount-aggfunction.md#estimation-accuracy)。

## <a name="syntax"></a>語法

`hll_merge(`*運算式 1* `,`*運算式 2*`, ...)`

## <a name="arguments"></a>引數

* 具有 `hll` 要合併之值的資料行。

## <a name="returns"></a>傳回

將資料行 [...] 合併為 `*Exrp1*` `*Expr2*` `*ExprN*` 一個 `hll` 值的結果。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|`dcount_hll_merged`|
|---|
|20|
