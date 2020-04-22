---
title: parse_json)- Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的parse_json()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87c2155864f039fb5b261786d0fa6eb6770af2f
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744688"
---
# <a name="parse_json"></a>parse_json()

將 解`string`譯為 JSON 值`dynamic`,並將該值傳回為 。 

當您需要提取 JSON 複合物件的多個元素時,它優於使用[extractjson() 函數](./extractjsonfunction.md)。

**語法**

`parse_json(`*Json*`)`

別名：
- [todynamic()](./todynamicfunction.md)
- [到物件()](./todynamicfunction.md)

**引數**

* *json*:`string`類型 運算式,表示[JSON 格式的值](https://json.org/),或表示實際`dynamic`值的類型[動態](./scalar-data-types/dynamic.md)運算式。

**傳回**

由`dynamic`*json*的值確定的類型物件:
* 如果*json*`dynamic`的類型 ,則按"正"形式使用其值。
* 如果*json*`string`的類型 為 ,並且是[格式正確的 JSON 字串](https://json.org/),則將分析該字串並返回生成的值。
* 如果*json*`string`的類型 ,但它**不是**[正確格式化的 JSON 字串](https://json.org/),則`dynamic``string`返回的值是保存原始 值的類型物件。

**範例**

在下列範例中，當 `context_custom_metrics` 是類似下面內容的 `string` 時： 

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

然後以下 CSL`duration`片段 檢索物件中槽的值,並從中檢索兩個`duration.value`插`duration.min`槽`118.0`,`110.0`與 ( 和 。

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**注意事項**

有一個JSON字串來描述屬性包,其中一個"插槽"是另一個JSON字串,這是有點常見的。 例如：

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

在這種情況下,不僅需要調用`parse_json`兩次,而且還要確保在第二個調`tostring`用 中使用。 否則,對的第二個`parse_json`呼叫將簡單地將輸入按「按」方式傳遞給輸出,因為它聲明的類型`dynamic`是 :

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```