---
title: 'gettype ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明在 Azure 資料總管中 ( # A1 的 gettype。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8fc1c3949ef13e504de6ba76be1bd5e600926288
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244812"
---
# <a name="gettype"></a>gettype()

傳回其單一引數的執行時間型別。

執行時間類型可能與名義型別為之運算式的名義 (靜態) 類型不同 `dynamic` ; 在這種情況下，在 `gettype()` 顯示實際值的傳回類型時，可能會很有用， (值在記憶體) 中的編碼方式。

## <a name="syntax"></a>語法

`gettype(`*Expr*`)`

## <a name="returns"></a>傳回

字串，表示其單一引數的執行時間型別。

## <a name="examples"></a>範例

|運算式                          |傳回      |
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