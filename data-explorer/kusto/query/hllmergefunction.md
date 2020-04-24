---
title: hll_merge() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的hll_merge()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 10e726af4e9dd2e129b526f016a7c37dc0c99d50
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514088"
---
# <a name="hll_merge"></a>hll_merge()

合併 hll 結果(聚合版本的標[`hll_merge()`](hll-merge-aggfunction.md)段版本 )。

閱讀有關[基礎演演算法 *(H*yper*L*og*L*og) 和估計精度](dcount-aggfunction.md#estimation-accuracy)。

**語法**

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

**引數**

* 具有要合併的 hll 值的欄。

**傳回**

合並列`*Exrp1*`的結果`*Expr2*`, ...`*ExprN*`到一個 hll 值。

**範例**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|dcount_hll_merged|
|---|
|20|