---
title: dcount_hll （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 dcount_hll （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 1b1b0c2313f32044a7988e0992c00786885ce2aa
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550294"
---
# <a name="dcount_hll"></a>dcount_hll()

計算 hll 結果中的 dcount （由[hll](hll-aggfunction.md)或[hll_merge](hll-merge-aggfunction.md)所產生）。

閱讀[基礎演算法（*H*Yper*l*og*l*og）和估計精確度](dcount-aggfunction.md#estimation-accuracy)的相關資訊。

**語法**

`dcount_hll(`*Expr*`)`

**引數**

* *Expr*：由[hll](hll-aggfunction.md)或 hll 所產生的運算式[-merge](hll-merge-aggfunction.md)

**傳回**

*Expr*中每個值的相異計數

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|
