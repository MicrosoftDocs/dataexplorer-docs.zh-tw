---
title: parse_json （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 parse_json （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abe49795b7b997abf677fd0fafff10ae38787f44
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346332"
---
# <a name="parse_json"></a>parse_json()

將解讀 `string` 為 JSON 值，並以形式傳回值 `dynamic` 。

當您需要解壓縮 JSON 綜合物件的多個元素時，此函式優於[extractjson （）函數](./extractjsonfunction.md)。

## <a name="syntax"></a>語法

`parse_json(`*json*`)`

別名：
- [todynamic()](./todynamicfunction.md)
- [toobject()](./todynamicfunction.md)

## <a name="arguments"></a>引數

* *json*：類型為的運算式 `string` 。 它代表[JSON 格式的值](https://json.org/)，或[動態](./scalar-data-types/dynamic.md)類型的運算式，代表實際的 `dynamic` 值。

## <a name="returns"></a>傳回

類型的物件 `dynamic` ，由*json*的值決定：
* 如果*json*屬於型別 `dynamic` ，則其值會以-is 的方式使用。
* 如果*json*屬於類型 `string` ，且是[格式正確的 json 字串](https://json.org/)，則會剖析字串，並傳回所產生的值。
* 如果*json*屬於類型 `string` ，但它不是[格式正確的 json 字串](https://json.org/)，則傳回的值會是類型的物件 `dynamic` ，其中包含原始 `string` 值。

## <a name="example"></a>範例

在下列範例中，當 `context_custom_metrics` 是類似下面內容的 `string` 時：

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

然後，下列的 CSL 片段 `duration` 會抓取物件中位置的值，並從它抓取兩個位置， `duration.value` 分別是 `duration.min` （ `118.0` 和 `110.0` ）。

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**備註**

JSON 字串通常會描述屬性包，其中其中一個「位置」是另一個 JSON 字串。 

例如：

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

在這種情況下，您不需要叫 `parse_json` 用兩次，也可以確保使用第二個呼叫 `tostring` 。 否則，的第二個呼叫 `parse_json` 只會依原本傳遞至輸出的輸入，因為它的宣告型別是 `dynamic` 。

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
