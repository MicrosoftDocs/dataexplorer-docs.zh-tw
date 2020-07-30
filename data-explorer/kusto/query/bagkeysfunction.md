---
title: bag_keys （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 bag_keys （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4ce05f536b8903810739c6aa7780f9eaf72c4f87
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349324"
---
# <a name="bag_keys"></a>bag_keys()

列舉動態屬性包物件中的所有根機碼。

`bag_keys(`*動態物件*`)`

## <a name="returns"></a>傳回

索引鍵的陣列，順序是不確定的。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```
datatable(index:long, d:dynamic) [
1, dynamic({'a':'b', 'c':123}), 
2, dynamic({'a':'b', 'c':{'d':123}}),
3, dynamic({'a':'b', 'c':[{'d':123}]}),
4, dynamic(null),
5, dynamic({}),
6, dynamic('a'),
7, dynamic([])]
| extend keys = bag_keys(d)
```

|索引|d|金鑰|
|---|---|---|
|1|{<br>  "a"： "b"，<br>  "c"：123<br>}|[<br>  "a"、<br>  "c"<br>]|
|2|{<br>  "a"： "b"，<br>  "c"： {<br>    "d"：123<br>  }<br>}|[<br>  "a"、<br>  "c"<br>]|
|3|{<br>  "a"： "b"，<br>  "c"： [<br>    {<br>      "d"：123<br>    }<br>  ]<br>}|[<br>  "a"、<br>  "c"<br>]|
|4|||
|5|{}|[]|
|6|a||
|7|[]||
