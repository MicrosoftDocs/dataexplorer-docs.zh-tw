---
title: '最小 ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的最小 ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: c6cd5b219b1f89e68d34b37b21135e5ec02d6ee8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248938"
---
# <a name="min-aggregation-function"></a>min ( # A1 (彙總函式) 

傳回整個群組的最小值。 

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

`summarize``min(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中 *Expr* 的最小值。
 
> [!TIP]
> 這可提供您自己的最小或最大值，例如，最高或最低價格。 但是，如果您想要資料列中的其他資料行（例如，使用最低價格的供應商名稱）， [arg_max](arg-max-aggfunction.md) 或 [arg_min](arg-min-aggfunction.md)。