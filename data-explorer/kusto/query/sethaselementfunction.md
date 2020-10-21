---
title: 'set_has_element ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 set_has_element。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: b32fd7361d98c47c8e93814db6b794a762fad1cf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247196"
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
