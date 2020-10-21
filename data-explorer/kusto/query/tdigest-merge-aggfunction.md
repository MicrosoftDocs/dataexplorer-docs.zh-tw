---
title: 'tdigest_merge ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) tdigest_merge。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 14b6b1c5ed31c312065e7641783bd1dc524993d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250632"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge ( # A1 (彙總函式) 

合併整個群組的 tdigest 結果。 

* 只能用在 [摘要內匯總](summarizeoperator.md)的內容中。

深入瞭解基礎演算法 (T 摘要) 和 [此處](percentiles-aggfunction.md#estimation-error-in-percentiles)的預估錯誤。

## <a name="syntax"></a>語法

摘要 `tdigest_merge(` *運算式* `)` 。

摘要 `tdigest_merge(` *Expr* `)` -別名。

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中的 *Expr* 合併 tdigest 值。
 

**提示**

1) 您可以使用函數 [ `percentile_tdigest()` ] (percentile-tdigestfunction.md) 。

2) 包含在相同群組中的所有 tdigests 都必須屬於相同類型。