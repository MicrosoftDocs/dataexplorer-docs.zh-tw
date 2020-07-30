---
title: varianceif （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 varianceif （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf1009d2d269bf21ea5ae14a9c828724d8bf8c70
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338468"
---
# <a name="varianceif-aggregation-function"></a>varianceif （）（彙總函式）

*計算述詞評估為*之整個群組的*Expr* [變異數](variance-aggfunction.md) `true` 。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

總結 `varianceif(` *Expr* `, ` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 
* 述*詞：如果*為 true，則*Expr*計算值會加入至變異數。

## <a name="returns"></a>傳回

在述*詞評估為*的整個群組中， *Expr*的變異數值 `true` 。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|