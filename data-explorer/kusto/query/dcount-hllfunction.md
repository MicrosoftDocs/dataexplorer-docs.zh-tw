---
title: 'dcount_hll ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 dcount_hll。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 743f35b6bf6d461c1d08c3082bb235b88b57ada2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252379"
---
# <a name="dcount_hll"></a>dcount_hll()

計算由 [hll](hll-aggfunction.md) 或 [hll_merge](hll-merge-aggfunction.md)) 產生之 hll 結果 (的 dcount。

深入瞭解 [基礎演算法 (*H*Yper*l*og*l*og) 和估計精確度](dcount-aggfunction.md#estimation-accuracy)。

## <a name="syntax"></a>語法

`dcount_hll(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*： [hll](hll-aggfunction.md) 或 hll 所產生的運算式 [-merge](hll-merge-aggfunction.md)

## <a name="returns"></a>傳回

*運算式*中每個值的相異計數

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
