---
title: tdigest_merge （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 tdigest_merge （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 6f15e0028bda40a2d65349a7840861c9060ff805
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87341239"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge （）（彙總函式）

將 tdigest 結果合併到群組中。 

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中。

深入瞭解基礎演算法（T-摘要式）和[此處](percentiles-aggfunction.md#estimation-error-in-percentiles)的估計錯誤。

## <a name="syntax"></a>語法

總結 `tdigest_merge(` *Expr* `)` 。

總結 `tdigest_merge(` *Expr* `)` -別名。

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

跨群組的*Expr*合併 tdigest 值。
 

**提示**

1) 您可以使用函數 [ `percentile_tdigest()` ] （percentile-tdigestfunction.md）。

2) 包含在相同群組中的所有 tdigests 都必須屬於相同的類型。