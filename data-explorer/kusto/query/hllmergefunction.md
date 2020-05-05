---
title: hll_merge （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 hll_merge （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 35dab83a658b714a220c7fbd6ff751627c0741e4
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741796"
---
# <a name="hll_merge"></a>hll_merge()

合併`hll`結果（匯總版本[`hll_merge()`](hll-merge-aggfunction.md)的純量版本）。

閱讀[基礎演算法（*H*Yper*l*og*l*og）和估計精確度](dcount-aggfunction.md#estimation-accuracy)的相關資訊。

**語法**

`hll_merge(`*運算式*`,` *運算式 2*`, ...)`

**引數**

* 具有`hll`要合併之值的資料行。

**傳回**

合併資料行`*Exrp1*`的結果， `*Expr2*`.。。`*ExprN*`到一個`hll`值。

**範例**

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