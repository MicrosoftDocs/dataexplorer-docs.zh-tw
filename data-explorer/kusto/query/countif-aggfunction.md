---
title: countif （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 countif （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5b69b50c0cf4c07934d7900937675bf6338eab9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348763"
---
# <a name="countif-aggregation-function"></a>countif （）（彙總函式）

傳回 Predicate** 評估為 `true` 的資料列計數。

* 只能在[匯總](summarizeoperator.md)的內容中使用

另請參閱- [count （）](count-aggfunction.md)函數，這會計算不含述詞運算式的資料列。

## <a name="syntax"></a>語法

摘要 `countif(` *Predicate*述詞`)`

## <a name="arguments"></a>引數

* 述*詞：將*用於匯總計算的運算式。 

## <a name="returns"></a>傳回

傳回 Predicate** 評估為 `true` 的資料列計數。

> [!TIP]
> 使用 `summarize countif(filter)` 來取代 `where filter | summarize count()`