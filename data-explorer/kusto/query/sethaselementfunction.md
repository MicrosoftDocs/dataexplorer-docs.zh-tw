---
title: 'set_has_element ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 set_has_element。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: b62e5032d6f2ccedc2883b6cbccaf7be69e1cebf
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103516"
---
# <a name="set_has_element"></a>set_has_element()

判斷指定的集合是否包含指定的元素。

## <a name="syntax"></a>語法

`set_has_element(`*陣列*、*值*`)`

## <a name="arguments"></a>引數

* *array*：要搜尋的輸入陣列。
* *value*：要搜尋的值。 值的類型必須是、、、、、、 `long` `integer` `double` `datetime` `timespan` `decimal` `string` 或 `guid` 。

## <a name="returns"></a>傳回

True 或 false，取決於值是否存在於陣列中。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|結果|
|---|
|1|

## <a name="see-also"></a>另請參閱

用 [`array_index_of(arr, value)`](arrayindexoffunction.md) 來尋找值存在於陣列中的位置。 這兩個函式的效能也相同。
