---
title: tdigest_merge() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 azure 數據資源管理器中的 tdigest_merge()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 988d7f05791723a823a5850f6865a780477f7bd4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506370"
---
# <a name="tdigest_merge"></a>tdigest_merge()

合併摘要結果(聚合版本的標段版本[`tdigest_merge()`](tdigest-merge-aggfunction.md))。

此專案可以閱讀更多有關基礎演演算法 (T-Digest) 和估計誤差[。](percentiles-aggfunction.md#estimation-error-in-percentiles)

**語法**

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` - 別名。

**引數**

* 具有要合併的 tdigest 的列。

**傳回**

合並列`*Expr1*`的結果`*Expr2*`, ...`*ExprN*`到一個消化。

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