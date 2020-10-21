---
title: '最大 ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (聚合) 函數的最大值。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cd53f0701064d95ab2d088e774f49b06f8d323f7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251060"
---
# <a name="max-aggregation-function"></a> ( # A1 (彙總函式的最大值) 

傳回整個群組的最大值。 

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

`summarize``max(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中 *Expr* 的最大值。
 
> [!TIP]
> 這可提供您自己的最小或最大值，例如，最高或最低價格。
> 但是，如果您想要資料列中的其他資料行（例如，使用最低價格的供應商名稱）， [arg_max](arg-max-aggfunction.md) 或 [arg_min](arg-min-aggfunction.md)。