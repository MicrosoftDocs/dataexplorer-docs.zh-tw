---
title: series_not_equals() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_not_equals()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ce9c695ececf1ac9f1fbe783ebe0fa35986f3d0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508172"
---
# <a name="series_not_equals"></a>series_not_equals()

計算兩個數位系列輸入的元素不等於`!=`( ) 邏輯操作。

**語法**

`series_not_equals (`*列1* `,` *系列2*`)`

**引數**

* *系列 1,系列 2*:輸入數值陣列以按元素進行比較。 所有參數必須是動態陣組。 

**傳回**

包含計算元素的布爾的動態陣列,在兩個輸入之間不相等邏輯操作。 任何非數位元素或不存在的元素(不同大小的陣列)都生成`null`元素值。

**範例**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_not_equals_s2 = series_not_equals(s1, s2)
```

|s1|s2|s1_not_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[真,假,真]|

**另請參閱**

有關整個系列統計資訊比較,請參閱:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)