---
title: 'parse_xml ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 parse_xml。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f003c5e9c6733391d61a2130528c9babc4aae67
ms.sourcegitcommit: d157e661de293aa4c2b5ad334a554eda0295bd2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91886293"
---
# <a name="parse_xml"></a>parse_xml()

`string`將視為 XML 值，會將值轉換為 JSON，並傳回做為的值 `dynamic` 。

## <a name="syntax"></a>語法

`parse_xml(`*xml*`)`

## <a name="arguments"></a>引數

* *xml*：類型的運算式 `string` ，表示 xml 格式的值。

## <a name="returns"></a>傳回

[動態](./scalar-data-types/dynamic.md)類型的物件，由*xml*的值決定，如果 xml 格式無效，則為 null。

轉換的完成方式如下：

XML                                |JSON                                            |存取
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | {"e"： null}                                  | o. e
`<e>text</e>`                      | {"e"： "text"}                                | o. e
`<e name="value" />`               | {"e"： {" @name "： "value"}}                     | o. e [" @name "]
`<e name="value">text</e>`         | {"e"： {" @name "： "value"，"#text"： "text"}} | o. e [" @name "] o. e ["#text"]
`<e> <a>text</a> <b>text</b> </e>` | {"e"： {"a"： "text"，"b"： "text"}}          | e. o. e. e. b
`<e> <a>text</a> <a>text</a> </e>` | {"e"： {"a"： ["text"，"text"]}}             | o. e. e. a [0] o. e. a [1]
`<e> text <a>text</a> </e>`        | {"e"： {"#text"： "text"，"a"： "text"}}      | 1 ' o. e ["#text"] o. e. a

**備註**

* 的最大輸入 `string` 長度 `parse_xml` 為 1mb (1048576 個位元組) 。 較長的字串轉譯將會產生 null 物件
* 只有元素節點、屬性和文位元組點會轉譯。 所有其他專案都會略過
 
## <a name="example"></a>範例

在下列範例中，當 `context_custom_metrics` 是類似下面內容的 `string` 時： 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<duration>
    <value>118.0</value>
    <count>5.0</count>
    <min>100.0</min>
    <max>150.0</max>
    <stdDev>0.0</stdDev>
    <sampledValue>118.0</sampledValue>
    <sum>118.0</sum>
</duration>
```

然後，下列的 CSL 片段會將 XML 轉譯為下列 JSON：

```json
{
    "duration": {
        "value": 118.0,
        "count": 5.0,
        "min": 100.0,
        "max": 150.0,
        "stdDev": 0.0,
        "sampledValue": 118.0,
        "sum": 118.0
    }
}
```

並抓取物件中位置的值 `duration` ，並從該位置抓取兩個位置， `duration.value` `duration.min` `118.0` `100.0`) 分別 (和。

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```
