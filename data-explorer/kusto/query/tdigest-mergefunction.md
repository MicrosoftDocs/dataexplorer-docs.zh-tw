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
ms.openlocfilehash: 92dce1a98cc0e24dcfbfcd7cb875fa370e3ae1d0
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737720"
---
# <a name="tdigest_merge"></a>tdigest_merge()

合併`tdigest`結果（匯總版本[`tdigest_merge()`](tdigest-merge-aggfunction.md)的純量版本）。

深入瞭解基礎演算法（T-摘要式）和[此處](percentiles-aggfunction.md#estimation-error-in-percentiles)的估計錯誤。

**語法**

`merge_tdigests(`*運算式*`,` *運算式 2*`, ...)`

`tdigest_merge(`*運算式*`,` *運算式 2* `, ...)` -別名。

**引數**

* 具有要合併之`tdigest`值的資料行。

**傳回**

合併資料行`*Expr1*`的結果， `*Expr2*`.。。`*ExprN*`一個`tdigest`。

**範例**

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