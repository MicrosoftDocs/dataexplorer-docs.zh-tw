---
title: hll_merge （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 hll_merge （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 4681f92155181f85cad5c46ed70a79cb173d437f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347539"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge （）（彙總函式）

`HLL`將整個群組的結果合併成單一 `HLL` 值。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中。

如需詳細資訊，請參閱[基礎演算法（*H*Yper*l*og*l*og）和估計精確度](dcount-aggfunction.md#estimation-accuracy)。

## <a name="syntax"></a>語法

`summarize``hll_merge(` *Expr*`)`

## <a name="arguments"></a>引數

* `*Expr*`：將用於匯總計算的運算式。

## <a name="returns"></a>傳回

函數會傳回 `hll` 整個群組的合併值 `*Expr*` 。
 
**提示**

1) 使用函數 [dcount_hll] （dcount-hllfunction.md）來計算 `dcount` from `hll`  /  `hll-merge` 彙總函式。
