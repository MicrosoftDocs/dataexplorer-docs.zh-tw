---
title: hll （）（彙總函式）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 hll （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: e602a920dd07089f688f39115805a2f99d505c9c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347556"
---
# <a name="hll-aggregation-function"></a>hll （）（彙總函式）

會計算整個群組的中繼結果 [`dcount`](dcount-aggfunction.md) ，而只會在[摘要內匯總](summarizeoperator.md)的內容中。

閱讀[基礎演算法（*H*Yper*l*og*l*og）和估計精確度](dcount-aggfunction.md#estimation-accuracy)的相關資訊。

## <a name="syntax"></a>語法

`summarize hll(`*`Expr`* `[,` *`Accuracy`*`])`

## <a name="arguments"></a>引數

* *`Expr`*：將用於匯總計算的運算式。 
* *`Accuracy`*（如果指定的話）會控制速度和精確度之間的平衡。

  |精確度值 |精確度  |速度  |錯誤  |
  |---------|---------|---------|---------|
  |`0` | lowest | 廣泛 | 1.6% |
  |`1` | 預設  | 對稱 | 0.8% |
  |`2` | high | slow | 0.4%  |
  |`3` | high | slow | 0.28% |
  |`4` | 極高 | 最 | 0.2% |
    
## <a name="returns"></a>傳回

跨群組之相異計數的中繼結果 *`Expr`* 。
 
**提示**

1. 您可以使用彙總函式 [`hll_merge`](hll-merge-aggfunction.md) 來合併一個以上 `hll` 的中繼結果（僅適用于 `hll` 輸出）。

1. 您可以使用函數來 [`dcount_hll`](dcount-hllfunction.md) 計算 `dcount` from `hll`  /  `hll_merge` 彙總函式。

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
