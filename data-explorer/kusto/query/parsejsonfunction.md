---
title: parse_json() - Azure 資料總管
description: 本文說明 Azure 資料總管中的 parse_json()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 3125a51733f6672d041e6c1522ea755e5677cb0c
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512854"
---
# <a name="parse_json"></a>parse_json()

將 `string` 解譯為 JSON 值，並以 `dynamic` 形式傳回值。

當您需要擷取 JSON 複合物件的多個元素時，此函式比 [extractjson()](./extractjsonfunction.md) 函式會更合適。

## <a name="syntax"></a>語法

`parse_json(`*json*`)`

別名：
- [todynamic()](./todynamicfunction.md)
- [toobject()](./todynamicfunction.md)

## <a name="arguments"></a>引數

* *json*：`string` 類型的運算式。 其代表 [JSON 格式的值](https://json.org/)，或類型為 [dynamic](./scalar-data-types/dynamic.md) 的運算式，並代表實際的 `dynamic` 值。

## <a name="returns"></a>傳回

`dynamic` 類型的物件，其取決於 *json* 的值：
* 如果 *json* 的類型為 `dynamic`，其值會依現狀使用。
* 如果 *json* 的類型為 `string`，而且是[正確格式的 JSON 字串](https://json.org/)，則會剖析字串並傳回所產生的值。
* 如果 *json* 的類型為 `string`，但不是[正確格式的 JSON 字串](https://json.org/)，則傳回的值是類型為 `string` 且保有原始 `dynamic` 值的物件。

## <a name="example"></a>範例

在下列範例中，當 `context_custom_metrics` 是類似下面內容的 `string` 時：

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

則下列 CSL 片段會擷取物件中 `duration` 位置的值，並從中擷取兩個位置：`duration.value` 和 `duration.min` (分別為 `118.0` 和 `110.0`)。

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**注意事項**

通常會有一個描述屬性包的 JSON 字串，其中的一個「位置」是另一個 JSON 字串。 

例如：

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

在這種情況下，不只需要叫用 `parse_json` 兩次，也務必在第二次呼叫中使用 `tostring`。 否則，對 `parse_json` 的第二次呼叫只會依原狀將輸入傳遞至輸出，因為其宣告的類型是 `dynamic`。

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
