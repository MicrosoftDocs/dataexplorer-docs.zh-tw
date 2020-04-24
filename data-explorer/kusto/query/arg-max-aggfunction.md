---
title: arg_max() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的arg_max()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 73953a17b1819081c8458d5dbde3fa6d55c8866e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518950"
---
# <a name="arg_max-aggregation-function"></a>arg_max() (聚合函數)

在組中查找最大化*ExprTo 最大化*的行,並返回*ExprToReturn*`*`的值(或返回整個行)。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize`【`(`*名稱exprto最大化*`,`*名稱exprto傳回*】`,` ...`)=` `(` `*` | `,` *ExprToMaximize*[ exprTO 最大化 , *Exprtoto 傳回*】 . `arg_max``)`

**引數**

* *ExprTo 最大化*:將用於聚合計算的運算式。 
* *ExprToReturn*:當*ExprToMaximize*為最大值時將用於返回值的運算式。 要返回的運算式可以是通配符 (*), 用於返回輸入表的所有列。
* *NameExprTo 最大化*:表示*ExprTo 最大化*的結果列的可選名稱。
* *NameExprToReturn:* 表示*ExprToReturn*的結果列的其他可選名稱。

**傳回**

在組中查找最大化*ExprTo 最大化*的行,並返回*ExprToReturn*`*`的值(或返回整個行)。

**範例**

有關[arg_min()聚合](arg-min-aggfunction.md)函數的範例