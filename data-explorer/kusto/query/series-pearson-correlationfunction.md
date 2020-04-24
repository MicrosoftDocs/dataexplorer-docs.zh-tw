---
title: series_pearson_correlation() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 azure 數據資源管理器中的 series_pearson_correlation()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 6454ec528e7a9e53b2feab5a7fefa1236ed80cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508104"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

計算兩個數位系列輸入的培生相關係數。

參見:[皮爾遜相關係數](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)。

**語法**

`series_pearson_correlation(`*列1* `,` *系列2*`)`

**引數**

* *系列1,系列2:* 用於計算相關係數的輸入數值陣列。 所有參數必須是長度相同的動態數位。 

**傳回**

兩個輸入之間的計算 Pearson 相關係數。 任何非數字元素或不存在的元素(不同大小的陣列)都會產生結果`null`。

**範例**

```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1,2,3,4,5]|[2,4,6,8,10]|1|
