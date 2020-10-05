---
title: 'bag_remove_keys ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 bag_remove_keys。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/04/2020
ms.openlocfilehash: fffbda419ac123af8e2744b76c7acbe560c07ce9
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712336"
---
# <a name="bag_remove_keys"></a>bag_remove_keys ( # A1

從屬性包中移除索引鍵和相關聯的值 `dynamic` 。

## <a name="syntax"></a>語法

`bag_remove_keys(`*包* `, `*金鑰*`)`

## <a name="arguments"></a>引數

* *包*： `dynamic` 屬性包輸入。
* 索引*鍵*： `dynamic` 陣列包含要從輸入中移除的索引鍵。 索引鍵是指屬性包的第一層。
不支援在嵌套層級上指定索引鍵。

## <a name="returns"></a>傳回值

傳回 `dynamic` 屬性包，但不含指定的索引鍵和其值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(input:dynamic)
[
    dynamic({'key1' : 123,     'key2': 'abc'}),
    dynamic({'key1' : 'value', 'key3': 42.0}),
]
| extend result=bag_remove_keys(input, dynamic(['key2', 'key4']))
```

|input|result|
|---|---|
|{<br>  "key1"：123，<br>  "key2"： "abc"<br>}|{<br>  "key1"：123<br>}|
|{<br>  "key1"： "value"，<br>  "key3"：42。0<br>}|{<br>  "key1"： "value"，<br>  "key3"：42。0<br>}|
