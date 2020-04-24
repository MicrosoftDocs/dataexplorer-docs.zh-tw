---
title: gettype() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 gettype()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3a28032320948f12b2f91febc9f59c7b35ad084e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514377"
---
# <a name="gettype"></a>gettype()

返回其單個參數的運行時類型。

對於標稱類型為`dynamic`的運算式,運行時類型可能與標稱類型(靜態)類型不同。在這種情況下`gettype()`,可以有助於揭示實際值的類型(該值在記憶體中的編碼方式)。

**語法**

`gettype(`*Expr*`)`

**傳回**

表示其單個參數的運行時類型的字串。

**範例**

|運算是                          |傳回值      |
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