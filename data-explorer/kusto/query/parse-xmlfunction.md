---
title: parse_xml() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的parse_xml()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ace60d0f9652d75b0f55cce4b1b622af20b42dc1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663684"
---
# <a name="parse_xml"></a>parse_xml()

將 解`string`譯為 XML 值,將數值為 JSON`dynamic`並將該值傳回為 。

**語法**

`parse_xml(`*Xml*`)`

**引數**

* *xml*:`string`表示 XML 格式值的類型運算式。

**傳回**

類型[動態](./scalar-data-types/dynamic.md)的物件,由*xml*的值或 null(如果 XML 格式無效)確定。

將XML轉換為JSON是使用[xml2json](https://github.com/Cheedoong/xml2json)庫完成的。

轉換完成如下:

XML                                |JSON                                            |存取
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | [ "e": null |                                  | o.e
`<e>text</e>`                      | [ "e":" 文字" |                                | o.e
`<e name="value" />`               | [ "e":\""@name值"|                     | o.e_"@name" ]
`<e name="value">text</e>`         | [ "e":@name" "值" #text":"文本" | | o.e_"@name"o.e_"#text"]
`<e> <a>text</a> <b>text</b> </e>` | [ "e": { "a":"文本","b":"文本" |          | o.e.a o.e.b
`<e> <a>text</a> <a>text</a> </e>` | [ "e": { "a": [文本] "文字"*             | o.e.a[0] o.e.a[1]
`<e> text <a>text</a> </e>`        | [ "e": " #text":"文本"," a":" 文本" |      | 1'o.e_"#text"]

**注意事項**

* 的最大輸入`string`長度`parse_xml`為 128 KB。 較長字串解釋會導致空物件 
* 將僅翻譯元素節點、屬性和文本節點。 其他一切都將被跳過
 
**範例**

在下列範例中，當 `context_custom_metrics` 是類似下面內容的 `string` 時：
<!--check this code formatting-->

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

然後以下 CSL 片段將 XML 轉換為以下 JSON:

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

並檢索`duration`物件中槽的值,並從中檢索兩個插`duration.value`槽`duration.min`和`118.0`(`110.0`和 。

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```