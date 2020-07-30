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
ms.openlocfilehash: 2e3847f0ad6c120f076461c5b4774f60349d6125
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348423"
---
# <a name="dcount_hll"></a>dcount_hll()

計算 hll 結果中的 dcount （由[hll](hll-aggfunction.md)或[hll_merge](hll-merge-aggfunction.md)所產生）。

閱讀[基礎演算法（*H*Yper*l*og*l*og）和估計精確度](dcount-aggfunction.md#estimation-accuracy)的相關資訊。

## <a name="syntax"></a>語法

`dcount_hll(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：由[hll](hll-aggfunction.md)或 hll 所產生的運算式[-merge](hll-merge-aggfunction.md)

## <a name="returns"></a>傳回

*Expr*中每個值的相異計數

## <a name="examples"></a>範例

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
