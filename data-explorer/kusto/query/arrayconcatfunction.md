---
title: array_concat() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 array_concat()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c66c17ab147eb3d6c5f749e7f28fad347a50ce22
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518746"
---
# <a name="array_concat"></a>array_concat()

將多個動態陣列與單個數位串聯。

**語法**

`array_concat(`*arr1*`[`` *arr2*, ...]`, )

**引數**

* *阿爾1...arrN*: 要串聯到動態陣列的輸入陣列。 所有參數都必須是動態陣列(請參閱[pack_array](packarrayfunction.md))。 

**傳回**

具有 arr1、arr2、...、 arrN 的陣列的動態陣列陣列。

**範例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|Column1|
|---|
|[1,2,4,1,2]|
|[2,4,8,2,4]|
|[3,6,12,3,6]|