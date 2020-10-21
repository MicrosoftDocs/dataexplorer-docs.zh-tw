---
title: 'array_split ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 array_split。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: b1dad77bd58d64bfcd167cc7d30f0986a54f13c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252803"
---
# <a name="array_split"></a>array_split()

根據分割索引將陣列分割成多個陣列，並將產生的陣列封裝在動態陣列中。

## <a name="syntax"></a>語法

`array_split`(*`arr`*, *`indices`*)

## <a name="arguments"></a>引數

* *`arr`*：要分割的輸入陣列，必須是動態陣列。
* *`indices`*：整數或動態的整數陣列，具有分割索引 (零為基底) ，負值會轉換成 array_length + 值。

## <a name="returns"></a>傳回

動態陣列，包含 N + 1 陣列，其中的值位於範圍內 `[0..i1), [i1..i2), ... [iN..array_length)` `arr` ，其中 N 是輸入索引的數目，而 `i1...iN` 是索引。

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
