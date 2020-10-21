---
title: 'hll_merge ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) hll_merge。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: a6aa19e3a88e7338494d6f6fad0e23a5ab4a03e4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252300"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge ( # A1 (彙總函式) 

`HLL`將整個群組的結果合併成單一 `HLL` 值。

* 只能用在 [摘要內匯總](summarizeoperator.md)的內容中。

如需詳細資訊，請參閱 [基礎演算法 (*H*Yper*l*og*l*og) 和估計精確度](dcount-aggfunction.md#estimation-accuracy)。

## <a name="syntax"></a>語法

`summarize``hll_merge(` *Expr*`)`

## <a name="arguments"></a>引數

* `*Expr*`：將用於匯總計算的運算式。

## <a name="returns"></a>傳回

函數會傳回 `hll` 整個群組的合併值 `*Expr*` 。
 
**提示**

1) 使用函式 [dcount_hll] (dcount-hllfunction.md) `dcount` 從 `hll`  /  `hll-merge` 彙總函式計算。
