---
title: make_list_with_nulls() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的make_list_with_nulls()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 4b039008c5969cf02187d69a3486b09e04ec41ae
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512864"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls() (聚合函數)

`dynamic`返回組中*Expr*的所有值 (JSON) 陣列,包括空值。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize``make_list_with_nulls(` *Expr*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。

**傳回**

`dynamic`返回組中*Expr*的所有值 (JSON) 陣列,包括空值。
如果未對`summarize`運算符的輸入進行排序,則生成的陣列中的元素順序將未定義。
如果對`summarize`運算符的輸入進行排序,則生成的陣列中的元素順序將追蹤輸入的順序。

> [!TIP]
> 使用[`mv-apply`](./mv-applyoperator.md)運算子按某些鍵建立有序清單。 請參閱[此處的](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)範例。
