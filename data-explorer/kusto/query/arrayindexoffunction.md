---
title: array_index_of （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 array_index_of （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: 27b956ee54ef22f55b3a0ceae97fceb41aadf5c3
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737346"
---
# <a name="array_index_of"></a>array_index_of()

在陣列中搜尋指定的專案，並傳回其位置。

**語法**

`array_index_of(`*陣列*，*值*`)`

**引數**

* *array*：要搜尋的輸入陣列。
* *value*：要搜尋的值。 此值的類型必須是 long、integer、double、datetime、timespan、decimal、string 或 guid。

**傳回**

Lookup 以零為起始的索引位置。
如果在陣列中找不到值，則傳回-1。

**範例**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|結果|
|---|
|3|

**另請參閱**

如果您只想要檢查某個值是否存在於陣列中，但您對其位置不感興趣，您可以使用[set_has_element （`arr`， `value`）](sethaselementfunction.md)。 此函式可改善查詢的可讀性。 這兩個函數都具有相同的效能。
