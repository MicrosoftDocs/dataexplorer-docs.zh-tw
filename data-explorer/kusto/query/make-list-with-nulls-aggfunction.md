---
title: 'make_list_with_nulls ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) make_list_with_nulls。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 18d10aa15e25979c0945ec26efb8bfe9a66dfde4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252216"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls ( # A1 (彙總函式) 

傳回 `dynamic` 群組中所有 *Expr* 值的 (JSON) 陣列，包含 null 值。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

`summarize``make_list_with_nulls(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。

## <a name="returns"></a>傳回

傳回 `dynamic` 群組中所有 *Expr* 值的 (JSON) 陣列，包含 null 值。
如果 `summarize` 未排序運算子的輸入，則產生之陣列中的專案順序是未定義的。
如果排序運算子的輸入 `summarize` ，則產生的陣列中的元素順序會追蹤輸入的專案順序。

> [!TIP]
> 使用 [`array_sort_asc()`](./arraysortascfunction.md) 或 [`array_sort_desc()`](./arraysortdescfunction.md) 函數，依某些索引鍵建立已排序的清單。
