---
title: 'make_list_with_nulls ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) make_list_with_nulls。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 1d4dafdab4c727b89838f18e13b016d771262f08
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89366005"
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
> 使用 [`mv-apply`](./mv-applyoperator.md) 運算子依某些索引鍵建立已排序的清單。 請參閱[此處的](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-make_list-aggregate-by-some-key)範例。
