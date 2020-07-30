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
ms.openlocfilehash: be993f3b0a58b56b9b4d171378bf71a645e77f1a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349511"
---
# <a name="array_split"></a>array_split()

根據分割索引，將陣列分割為多個陣列，並將產生的陣列封裝在動態陣列中。

## <a name="syntax"></a>語法

`array_split`(*`arr`*, *`indices`*)

## <a name="arguments"></a>引數

* *`arr`*：要分割的輸入陣列，必須是動態陣列。
* *`indices`*：具有分割索引（以零為基底）的整數或動態整數陣列，負數值會轉換成 array_length + 值。

## <a name="returns"></a>傳回

動態陣列，包含 N + 1 個數組，其中的值在範圍內 `[0..i1), [i1..i2), ... [iN..array_length)` `arr` ，其中 N 是輸入索引的數目，而 `i1...iN` 是索引。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```

|`arr`|`arr_split`|
|---|---|
|[1，2，3，4，5]|[[1，2]，[3，4，5]]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```

|`arr`|`arr_split`|
|---|---|
|[1，2，3，4，5]|[[1]，[2，3]，[4，5]]|
