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
ms.openlocfilehash: c53faca94e273bf816abcfa34ed400708a7433a3
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169552"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls ( # A1 (彙總函式) 

傳回 `dynamic` 群組中所有 *Expr* 值的 (JSON) 陣列，包含 null 值。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>Syntax

`summarize``make_list_with_nulls(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。

## <a name="returns"></a>傳回

傳回 `dynamic` 群組中所有 *Expr* 值的 (JSON) 陣列，包含 null 值。
如果 `summarize` 未排序運算子的輸入，則產生之陣列中的專案順序是未定義的。
如果排序運算子的輸入 `summarize` ，則產生的陣列中的元素順序會追蹤輸入的專案順序。

> [!TIP]
> 使用 [`array_sort_asc()`](./arraysortascfunction.md) 或 [`array_sort_desc()`](./arraysortdescfunction.md) 函數，依某些索引鍵建立已排序的清單。
