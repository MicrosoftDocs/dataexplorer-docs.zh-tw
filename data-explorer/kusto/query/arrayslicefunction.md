---
title: 'array_slice ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 array_slice。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ecb30d00a6c95e3754686eb264d9439c1c1e605f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246755"
---
# <a name="array_slice"></a>array_slice()

解壓縮動態陣列的磁區。

## <a name="syntax"></a>語法

`array_slice`(*`arr`*, *`start`*, *`end`*)

## <a name="arguments"></a>引數

* *`arr`*：要從中解壓縮配量的輸入陣列必須是動態陣列。
* *`start`*：以零為基底的 (（含）) 起點的起始索引，負值會轉換成 array_length + start。
* *`end`*：以零為基底的 (（含）) 結束索引，負數值會轉換成 array_length + end。

注意：會忽略超出範圍的索引。

## <a name="returns"></a>傳回

範圍 [] 中值的動態陣列 `start..end` `arr` 。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1，2，3]|[2，3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|切片|
|---|---|
|[1，2，3，4，5]|[3，4，5]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|切片|
|---|---|
|[1，2，3，4，5]|[3，4]|
