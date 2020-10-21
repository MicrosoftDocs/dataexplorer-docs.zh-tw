---
title: 'bag_keys ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 bag_keys。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f36022bb1e9d0f72f2f63e14be888c0f462ccc70
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245473"
---
# <a name="bag_keys"></a>bag_keys()

列舉動態屬性包物件中的所有根金鑰。

## <a name="syntax"></a>語法

`bag_keys(`*動態物件*`)`

## <a name="returns"></a>傳回

索引鍵的陣列，但不確定順序。

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
