---
title: gettype （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 gettype （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 0efa07b7a1b050fe81ce2f369e8df5af4c05e212
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347658"
---
# <a name="gettype"></a>gettype()

傳回其單一引數的執行時間類型。

執行時間類型可能與名義類型為之運算式的名義（靜態）類型不同 `dynamic` ; 在這種情況下 `gettype()` ，顯示實際值的建立類型（此值在記憶體中的編碼方式）會很有用。

## <a name="syntax"></a>語法

`gettype(`*Expr*`)`

## <a name="returns"></a>傳回

表示其單一引數之執行時間類型的字串。

## <a name="examples"></a>範例

|運算是                          |傳回      |
|------------------------------------|-------------|
|`gettype("a")`                      |`string`     |
|`gettype(111)`                      |`long`       |
|`gettype(1==1)`                     |`bool`       |
|`gettype(now())`                    |`datetime`   |
|`gettype(1s)`                       |`timespan`   |
|`gettype(parse_json('1'))`           |`int`        |
|`gettype(parse_json(' "abc" '))`     |`string`     |
|`gettype(parse_json(' {"abc":1} '))` |`dictionary` | 
|`gettype(parse_json(' [1, 2, 3] '))` |`array`      |
|`gettype(123.45)`                   |`real`       |
|`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))`|`guid`| 
|`gettype(parse_json(''))`            |`null`|