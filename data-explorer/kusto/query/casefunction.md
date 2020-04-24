---
title: 案例() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 case()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 479c4a99d410a2df7a608531914d7dccfc555e7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517267"
---
# <a name="case"></a>case()

計算謂詞清單並返回滿足謂詞的第一個結果表達式。

如果兩個謂詞均未返回`true`,則返回最後一個表達式`else`( 的 ) 的結果。
所有奇數參數(計數從 1 開始)必須是計算到`boolean`值的 運算式。
所有偶數參數 (s)`then`和最後一`else`個參數 () 必須具有相同的類型。

**語法**

`case(`*predicate_1* * * *else* *predicate_3* `,` *predicate_2* `,` * *predicate_1then_1, predicate_2then_2 , predicate_3then_3 , *then_1* `,``)`

**引數**

* *predicate_i*: 計算到`boolean`值的 運算式。
* *then_i*: 如果*predicate_i*是第`true`一個計算到 的謂詞,則獲取計算的運算式並將其值從 函數返回。
* *其他*:如果兩個*predicate_i*都`true`計算到 ,則從函數返回計算及其值的運算式。

**傳回**

第一*個then_i*的值,*predicate_i*其 predicate_i`true`計算為 ,或者如果兩個謂詞都未滿足,則*其他*值的值。

**範例**

```kusto
range Size from 1 to 15 step 2
| extend bucket = case(Size <= 3, "Small", 
                       Size <= 10, "Medium", 
                       "Large")
```

|大小|貯體|
|---|---|
|1|小型|
|3|小型|
|5|中|
|7|中|
|9|中|
|11|大型|
|13|大型|
|15|大型|
