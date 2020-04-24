---
title: series_equals() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_equals()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 267e9db8dd2980016f4022ce6358569f30aacb6f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508801"
---
# <a name="series_equals"></a>series_equals()

計算兩個數位系列輸入的元素等於`==`( ) 邏輯操作。

**語法**

`series_equals (`*列1* `,` *系列2*`)`

**引數**

* *系列 1,系列 2*:輸入數值陣列以按元素進行比較。 所有參數必須是動態陣組。 

**傳回**

包含計算元素在兩個輸入之間相等邏輯操作的布爾值的動態陣列。 任何非數位元素或不存在的元素(不同大小的陣列)都生成`null`元素值。

**範例**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_equals_s2 = series_equals(s1, s2)
```

|s1|s2|s1_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[虛假、真實、虛假]|

**另請參閱**

有關整個系列統計資訊比較,請參閱:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)