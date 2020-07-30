---
title: make_list_with_nulls （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 make_list_with_nulls （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: b78bed51da8422dced4d57406c10721639b68ccc
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346978"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls （）（彙總函式）

傳回 `dynamic` 群組中*Expr*所有值的（JSON）陣列，包括 null 值。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

`summarize``make_list_with_nulls(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。

## <a name="returns"></a>傳回

傳回 `dynamic` 群組中*Expr*所有值的（JSON）陣列，包括 null 值。
如果 `summarize` 未排序運算子的輸入，則產生之陣列中的專案順序會是未定義的。
如果對運算子的輸入 `summarize` 進行排序，則產生之陣列中的專案順序會追蹤輸入的內容。

> [!TIP]
> 使用 [`mv-apply`](./mv-applyoperator.md) 運算子，依某個索引鍵建立已排序的清單。 請參閱[此處的](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)範例。
