---
title: array_split （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 array_split （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 360a958a08b93d22dabd15b187f8227606486709
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737380"
---
# <a name="array_split"></a>array_split()

根據分割索引，將陣列分割為多個陣列，並將產生的陣列封裝在動態陣列中。

**語法**

`array_split`(*`arr`*, *`indices`*)

**引數**

* *`arr`*：要分割的輸入陣列，必須是動態陣列。
* *`indices`*：具有分割索引（以零為基底）的整數或動態整數陣列，負數值會轉換成 array_length + 值。

**傳回**

動態陣列`[0..i1), [i1..i2), ... [iN..array_length)` `arr`，包含 n + 1 個數組，其中的值在範圍內，其中 N 是輸入索引的數目`i1...iN` ，而是索引。

**範例**

```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```

|`arr`|`arr_split`|
|---|---|
|[1，2，3，4，5]|[[1，2]，[3，4，5]]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```

|`arr`|`arr_split`|
|---|---|
|[1，2，3，4，5]|[[1]，[2，3]，[4，5]]|
