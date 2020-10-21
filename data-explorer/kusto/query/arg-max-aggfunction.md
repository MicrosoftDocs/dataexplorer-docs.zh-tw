---
title: 'arg_max ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) arg_max。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: bd3d0b2c9d0c68ae646960b0b4b0b3ca41c0bb43
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248137"
---
# <a name="arg_max-aggregation-function"></a>arg_max ( # A1 (彙總函式) 

在群組中尋找可將 *ExprToMaximize*最大化的資料列，並傳回 *ExprToReturn* (的值，或傳回 `*` 整個資料列) 。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

`summarize`[ `(` *NameExprToMaximize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_max` `(`*ExprToMaximize*， `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>引數

* *ExprToMaximize*：將用於匯總計算的運算式。 
* *ExprToReturn*：當 *ExprToMaximize* 為最大值時，將用來傳回值的運算式。 要傳回的運算式可以是萬用字元 ( * ) 傳回輸入資料表的所有資料行。
* *NameExprToMaximize*：代表 *ExprToMaximize*之結果資料行的選擇性名稱。
* *NameExprToReturn*：代表 *ExprToReturn*之結果資料行的其他選擇性名稱。

## <a name="returns"></a>傳回

在群組中尋找可將 *ExprToMaximize*最大化的資料列，並傳回 *ExprToReturn* (的值，或傳回 `*` 整個資料列) 。

## <a name="examples"></a>範例

請參閱 [arg_min ( # B1 ](arg-min-aggfunction.md) 彙總函式的範例