---
title: 'parse_json ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 parse_json。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 3125a51733f6672d041e6c1522ea755e5677cb0c
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512854"
---
# <a name="parse_json"></a>parse_json()

將 `string` 做為 JSON 值，並傳回做為的值 `dynamic` 。

當您需要解壓縮 JSON 綜合物件的多個元素時，此函式優於 [extractjson ( # A1 函數](./extractjsonfunction.md) 。

## <a name="syntax"></a>語法

`parse_json(`*json*`)`

別名：
- [todynamic()](./todynamicfunction.md)
- [toobject ( # B1 ](./todynamicfunction.md)

## <a name="arguments"></a>引數

* *json*：類型為的運算式 `string` 。 它代表 [JSON 格式的值](https://json.org/)，或是 [動態](./scalar-data-types/dynamic.md)類型的運算式，表示實際 `dynamic` 值。

## <a name="returns"></a>傳回

由 `dynamic` *json* 值決定的型別的物件：
* 如果 *json* 的型別為 `dynamic` ，則其值會依原樣使用。
* 如果 *json* 的型別為 `string` ，而且是 [格式正確的 json 字串](https://json.org/)，則會剖析字串，並傳回產生的值。
* 如果 *json* 的類型是 `string` ，但它不是 [格式正確的 json 字串](https://json.org/)，則傳回的值會是 `dynamic` 具有原始值之類型的物件 `string` 。

## <a name="example"></a>範例

在下列範例中，當 `context_custom_metrics` 是類似下面內容的 `string` 時：

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

然後，下列 CSL 片段會抓取物件中位置的值 `duration` ，並從該位置抓取兩個位置， `duration.value` `duration.min` `118.0`) 分別 (和 `110.0` ）。

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**注意事項**

通常會有一個描述屬性包的 JSON 字串，其中其中一個「位置」是另一個 JSON 字串。 

例如：

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

在這種情況下，您不只需要 `parse_json` 叫用兩次，也可以確保使用第二個呼叫的 `tostring` 。 否則，的第二個呼叫 `parse_json` 只會將輸入依原樣傳遞給輸出，因為其宣告的型別為 `dynamic` 。

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
