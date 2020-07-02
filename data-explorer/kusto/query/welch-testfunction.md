---
title: welch_test （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 welch_test （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1bb995874bf6ac552350c602c6d3742a08b1273b
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763677"
---
# <a name="welch_test"></a>welch_test()

計算[Welch-test 函數](https://en.wikipedia.org/wiki/Welch%27s_t-test)的 p_value

```kusto
// s1, s2 values are from https://en.wikipedia.org/wiki/Welch%27s_t-test
print
    s1 = dynamic([27.5, 21.0, 19.0, 23.6, 17.0, 17.9, 16.9, 20.1, 21.9, 22.6, 23.1, 19.6, 19.0, 21.7, 21.4]),
    s2 = dynamic([27.1, 22.0, 20.8, 23.4, 23.4, 23.5, 25.8, 22.0, 24.8, 20.2, 21.9, 22.1, 22.9, 20.5, 24.4])
| mv-expand s1 to typeof(double), s2 to typeof(double)
| summarize m1=avg(s1), v1=variance(s1), c1=count(), m2=avg(s2), v2=variance(s2), c2=count()
| extend pValue=welch_test(m1,v1,c1,m2,v2,c2)

// pValue = 0.021
```

**語法**

`welch_test(`*mean1* `, `*variance1* `, `*count1* `, `*mean2* `, `*variance2* `, `*count2*`)`

**引數**

* *mean1*：代表第一個數列之平均值（平均）值的運算式
* *variance1*：表示第一個數列之變異數值的運算式
* *count1*：代表第一個數列中值計數的運算式
* *mean2*：代表第二個數列之平均值（平均）值的運算式
* *variance2*：表示第二個數列之變異數值的運算式
* *count2*：代表第二個數列中值計數的運算式

**傳回**

從[維琪百科](https://en.wikipedia.org/wiki/Welch%27s_t-test)：

在 [統計資料] 中，Welch 的 t-test 是兩個樣本的位置測試，用來測試兩個人口有相等的假設。 Welch 的 t 測試是一種調整學生的 t 測試，而且當兩個樣本的變異數不相等和不相等的樣本大小時，也更可靠。 這些測試通常稱為「未配對」或「獨立範例」 t-測試。 當比較兩個樣本基礎的統計單位不重迭時，通常會套用測試。 Welch 的 t 測試比學生的 t 測試更受歡迎，而且讀者可能比較不熟悉。 測試也稱為「Welch 的不相等差異 t-測試」或「不相等的差異 t-測試」。
