---
title: make_list() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的make_list(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e46dbfac7b8c819f66d469c160452ec4dddfb769
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512745"
---
# <a name="make_list-aggregation-function"></a>make_list() (聚合函數)

傳回群組中 Expr** 所有值的 `dynamic` (JSON) 陣列。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize``make_list(` *Expr* `,` [*最大尺寸*】`)`

**引數**

* *Expr*:將用於聚合計算的運算式。
* *MaxSize*是返回的最大元素數的可選整數限制(預設值為*1048576)。* 最大大小值不能超過 1048576。

> [!NOTE]
> 此函數的舊版和過時變體:`makelist()`預設限制為*MaxSize* = 128。

**傳回**

傳回群組中 Expr** 所有值的 `dynamic` (JSON) 陣列。
如果未對`summarize`運算符的輸入進行排序,則生成的陣列中的元素順序將未定義。
如果對`summarize`運算符的輸入進行排序,則生成的陣列中的元素順序將追蹤輸入的順序。

> [!TIP]
> 使用[`mv-apply`](./mv-applyoperator.md)運算子按某些鍵建立有序清單。 請參閱[此處的](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)範例。

**另請參閱**

[`make_list_if`](./makelistif-aggfunction.md)運算符與`make_list`類似,但它還接受謂詞。