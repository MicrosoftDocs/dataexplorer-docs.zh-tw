---
title: max （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 max （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4d31f2137fcd64deab713522b1596f4c572606cc
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346842"
---
# <a name="max-aggregation-function"></a>max （）（彙總函式）

傳回整個群組的最大值。 

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

`summarize``max(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中*Expr*的最大值。
 
> [!TIP]
> 這可為您提供其本身的最小值或最大值，例如最高或最低價格。
> 但是，如果您想要資料列中的其他資料行（例如，價格最低的供應商名稱），請使用[arg_max](arg-max-aggfunction.md)或[arg_min](arg-min-aggfunction.md)。