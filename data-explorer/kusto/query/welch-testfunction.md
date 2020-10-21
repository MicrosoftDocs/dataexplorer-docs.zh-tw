---
title: 'welch_test ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 welch_test。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 063e0db08b3566ed69957dda675082d8ea6942cf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253167"
---
# <a name="welch_test"></a>welch_test()

計算[Welch 測試函數](https://en.wikipedia.org/wiki/Welch%27s_t-test)的 p_value

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

## <a name="syntax"></a>語法

`welch_test(`*mean1* `, `*variance1* `, `*count1* `, `*mean2* `, `*variance2* `, `*count2*`)`

## <a name="arguments"></a>引數

* *mean1*：代表第一個數列的平均 (平均) 值的運算式
* *variance1*：代表第一個數列之變異數值的運算式
* *count1*：代表第一個數列中值計數的運算式
* *mean2*：代表第二個數列平均) 平均值 (的運算式
* *variance2*：代表第二個數列之變異數值的運算式
* *count2*：代表第二個數列中值計數的運算式

## <a name="returns"></a>傳回

從 [維琪百科](https://en.wikipedia.org/wiki/Welch%27s_t-test)：

在統計資料中，Welch 的 t-test 是雙樣本位置測試，用來測試兩個人口是否相等的假設。 Welch 的 t 測試是學生的 t 測試調整，而且當兩個樣本具有不相等的變異數和不相等的樣本大小時，更可靠。 這些測試通常稱為「不成對」或「獨立範例」的 t 測試。 測試通常會在兩個要比較之樣本的基礎單位不重迭時套用。 Welch 的 t 測試比學生的 t 測試更受歡迎，而且讀者可能較不熟悉。 測試也稱為「Welch 的不相等變異數 t 測試」或「不相等的差異 t 測試」。
