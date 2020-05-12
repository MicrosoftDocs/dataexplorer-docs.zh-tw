---
title: make_list （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 make_list （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: fed2616f5fbd32b1c80f936d5689261467a9dc5e
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224828"
---
# <a name="make_list-aggregation-function"></a>make_list （）（彙總函式）

傳回群組中 Expr** 所有值的 `dynamic` (JSON) 陣列。

* 只能在[匯總](summarizeoperator.md)的內容中使用

**語法**

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

**引數**

* *Expr*：將用於匯總計算的運算式。
* *MaxSize*是傳回的最大專案數目的選擇性整數限制（預設為*1048576*）。 MaxSize 值不能超過1048576。

> [!NOTE]
> 此函式的舊版和過時變體： `makelist()` 具有*MaxSize* = 128 的預設限制。

**傳回**

傳回群組中 Expr** 所有值的 `dynamic` (JSON) 陣列。
如果 `summarize` 未排序運算子的輸入，則產生之陣列中的專案順序會是未定義的。
如果對運算子的輸入 `summarize` 進行排序，則產生之陣列中的專案順序會追蹤輸入的內容。

> [!TIP]
> 使用 [`mv-apply`](./mv-applyoperator.md) 運算子，依某個索引鍵建立已排序的清單。 請參閱[此處的](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)範例。

**另請參閱**

[`make_list_if`](./makelistif-aggfunction.md)運算子類似于 `make_list` ，不同之處在于它也接受述詞。