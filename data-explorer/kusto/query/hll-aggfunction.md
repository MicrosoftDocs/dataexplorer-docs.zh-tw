---
title: 'hll ( # A1 (彙總函式) -Azure 資料總管'
description: '本文說明 Azure 資料總管中的 hll ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: 4474299c804e1b54d3060d639d171652e770d989
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241603"
---
# <a name="hll-aggregation-function"></a>hll ( # A1 (彙總函式) 

計算整個群組的中繼結果 [`dcount`](dcount-aggfunction.md) ，只在 [摘要內匯總](summarizeoperator.md)的內容中。

閱讀 [基礎演算法 (*H*Yper*l*og*l*og) 和估計精確度](dcount-aggfunction.md#estimation-accuracy)。

## <a name="syntax"></a>語法

`summarize hll(`*`Expr`* `[,` *`Accuracy`*`])`

## <a name="arguments"></a>引數

* *`Expr`*：將用於匯總計算的運算式。 
* *`Accuracy`* 如果有指定，就會控制速度和精確度之間的平衡。

  |精確度值 |精確度  |速度  |錯誤  |
  |---------|---------|---------|---------|
  |`0` | lowest | 快 | 1.6% |
  |`1` | default  | 平衡 | 0.8% |
  |`2` | high | slow | 0.4%  |
  |`3` | high | slow | 0.28% |
  |`4` | 額外高度 | 慢 | 0.2% |
    
## <a name="returns"></a>傳回

跨群組之相異計數的中繼結果 *`Expr`* 。
 
**提示**

1. 您可以使用彙總函式 [`hll_merge`](hll-merge-aggfunction.md) 來合併多個 `hll` 中繼結果 (只適用于 `hll` 輸出) 。

1. 您可以使用函數 [`dcount_hll`](dcount-hllfunction.md) ，這將會 `dcount` 從 `hll`  /  `hll_merge` 彙總函式計算。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|`hll_DamageProperty`|
|---|---|
|2007-09-18 20：00：00.0000000|[[1024，14]，[-5473486921211236216，-6230876016761372746，3953448761157777955，4246796580750024372]，[]]|
|2007-09-20 21：50：00.0000000|[[1024，14]，[4835649640695509390]，[]]|
|2007-09-29 08：10：00.0000000|[[1024，14]，[4246796580750024372]，[]]|
|2007-12-30 16：00：00.0000000|[[1024，14]，[4246796580750024372，-8936707700542868125]，[]]|
