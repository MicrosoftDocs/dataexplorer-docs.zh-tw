---
title: welch_test() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的welch_test()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9f4b3d86bf3d679fd4fdd320b394956c71d3e97
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504483"
---
# <a name="welch_test"></a>welch_test()

計算[韋爾奇測試函數](https://en.wikipedia.org/wiki/Welch%27s_t-test)p_value

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

`welch_test(`*平均1*`, `*方差1*`, `*計數1*`, `*平均2*`, `*方差2*`, `*計數2*`)`

**引數**

* *平均1:* 表示第 1 個系列的平均值(平均值)值的運算式
* *方差1:* 表示第 1 個系列的方差值的運算式
* *count1*: 表示第 1 個系列的值計數的運算式
* *平均2*:表示第 2 個系列的平均值(平均值)的運算式
* *方差2*:表示第二個系列的方差值的運算式
* *count2*: 表示第 2 個系列的值計數的運算式

**傳回**

從[維琪百科](https://en.wikipedia.org/wiki/Welch%27s_t-test):

在統計中,Welch 的 t 檢驗或不等差 t 檢驗是一個雙樣本位置檢驗,用於測試兩個總體具有相等均等均值的假設。 Welch 的 t 檢驗是學生 t 測試的適應,即它是在學生 t 測試的説明下派生的,當兩個樣本具有不等方差和樣本量不相等時,該測試更可靠。 這些測試通常稱為"未配對"或"獨立樣本"t 檢驗,因為當所比較的兩個樣本的基礎統計單位不重疊時,通常會應用這些測試。 鑒於 Welch 的 t 測試不如學生的 t-test 受歡迎,而且讀者可能不太熟悉,因此資訊更豐富的名稱是"韋爾奇的不等方差 t 檢驗"或"不均差異 t 檢驗",以便簡潔。