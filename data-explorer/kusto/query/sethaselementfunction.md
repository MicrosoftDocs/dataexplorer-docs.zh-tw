---
title: set_has_element （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 set_has_element （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 9cf2ec4371f4aeef8a68cb65fb2b946b9c393054
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372364"
---
# <a name="set_has_element"></a>set_has_element()

判斷指定的集合是否包含指定的元素。

**語法**

`set_has_element(`*陣列*，*值*`)`

**引數**

* *array*：要搜尋的輸入陣列。
* *value*：要搜尋的值。 值應為、、、、、、 `long` `integer` 或類型 `double` `datetime` `timespan` `decimal` `string` `guid` 。

**傳回**

[True] 或 [false]，視值是否存在於陣列中而定。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|結果|
|---|
|1|

**另請參閱**

如果您也對值存在於陣列中的位置感興趣，您可以使用[array_index_of （arr，value）](arrayindexoffunction.md)。 這兩個函數都是相同的效能。
