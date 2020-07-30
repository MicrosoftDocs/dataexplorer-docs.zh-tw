---
title: 案例（）-Azure 資料總管
description: 本文說明 Azure 資料總管中的案例（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 90942906908f58f321e5a81ec9ca8419fe9a2ec5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348916"
---
# <a name="case"></a>case()

評估述詞清單，並傳回滿足述詞的第一個結果運算式。

如果沒有傳回任何述詞 `true` ，則會傳回最後一個運算式的結果（ `else` ）。
所有奇數引數（從1開始的計數）必須是評估為值的運算式 `boolean` 。
所有偶數引數（ `then` s）和最後一個引數（ `else` ）都必須是相同的類型。

## <a name="syntax"></a>語法

`case(`*predicate_1* `,`*then_1*、 *predicate_2* `,` *then_2*、 *predicate_3* `,` *then_3*、*其他*`)`

## <a name="arguments"></a>引數

* *predicate_i*：評估為值的運算式 `boolean` 。
* *then_i*：如果*predicate_i*是評估為的第一個述詞，則會評估一個運算式，並從函式傳回它的值 `true` 。
* *否則*：如果*predicate_i*不會評估為，則會評估一個運算式，並從函式傳回它的值 `true` 。

## <a name="returns"></a>傳回

第一個*then_i*的值，其*predicate_i*評估為 `true` ，如果未滿足任何述詞，則為*else*的值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range Size from 1 to 15 step 2
| extend bucket = case(Size <= 3, "Small", 
                       Size <= 10, "Medium", 
                       "Large")
```

|大小|貯體|
|---|---|
|1|小|
|3|小|
|5|中|
|7|中|
|9|中|
|11|大|
|13|大|
|15|大|
