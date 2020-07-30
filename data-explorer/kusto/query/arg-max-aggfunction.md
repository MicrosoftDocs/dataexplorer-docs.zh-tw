---
title: arg_max （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 arg_max （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1ce8bb0635743fd6692cda3b707bbb7d88e07af9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349698"
---
# <a name="arg_max-aggregation-function"></a>arg_max （）（彙總函式）

在群組中尋找最大化*exprtomaximize 資料列*的資料列，並傳回*ExprToReturn*的值（或傳回 `*` 整個資料列）。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

`summarize`[ `(` *NameExprToMaximize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_max` `(`*Exprtomaximize 資料列*， `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>引數

* *Exprtomaximize 資料列*：將用於匯總計算的運算式。 
* *ExprToReturn*：將用來在*exprtomaximize 資料列*為 maximum 時傳回值的運算式。 要傳回的運算式可能是萬用字元（*），以傳回輸入資料表的所有資料行。
* *NameExprToMaximize*：代表*exprtomaximize 資料列*之結果資料行的選擇性名稱。
* *NameExprToReturn*：代表*ExprToReturn*之結果資料行的其他選擇性名稱。

## <a name="returns"></a>傳回

在群組中尋找最大化*exprtomaximize 資料列*的資料列，並傳回*ExprToReturn*的值（或傳回 `*` 整個資料列）。

## <a name="examples"></a>範例

請參閱[arg_min （）](arg-min-aggfunction.md)彙總函式的範例