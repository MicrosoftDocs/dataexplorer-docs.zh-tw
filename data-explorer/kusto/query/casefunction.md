---
title: '案例 ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 的案例。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 809c11f337db86e9b9bdfbd93439e5f743008c38
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249318"
---
# <a name="case"></a>case()

評估述詞的清單，並傳回滿足其述詞的第一個結果運算式。

如果沒有傳回任何述詞 `true` ，則會傳回最後一個運算式 () 的結果 `else` 。
所有奇數引數 (count 從1開始) 必須是評估為值的運算式  `boolean` 。
所有偶數引數 (`then` s) ，而 () 的最後一個引數 `else` 必須屬於相同的類型。

## <a name="syntax"></a>語法

`case(`*predicate_1*、 *then_1*、 *predicate_2*、 *then_2*、 *predicate_3*、 *then_3*、 *其他*`)`

## <a name="arguments"></a>引數

* *predicate_i*：評估為值的運算式 `boolean` 。
* *then_i*：如果 *predicate_i* 是評估為的第一個述詞，則會從函式傳回會評估的運算式，而且其值會從函數傳回 `true` 。
* *否則*：如果 *predicate_i* 不是評估為，就會從函式傳回會評估的運算式，以及其值 `true` 。

## <a name="returns"></a>傳回

第一個*predicate_i*評估為的*then_i*的值 `true` ，如果沒有滿足任何述詞，則為*else*的值。

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
