---
title: array_slice （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 array_slice （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: f2de8d91b73a3495c8b0902d710bd87c7fecbafa
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737550"
---
# <a name="array_slice"></a>array_slice()

解壓縮動態陣列的配量。

**語法**

`array_slice`(*`arr`*, *`start`*, *`end`*])

**引數**

* *`arr`*：要從中解壓縮配量的輸入陣列必須是動態陣列。
* *`start`*：配量以零為基底的起始索引，負值會轉換成 array_length + start。
* *`end`*：配量的以零為起始（含）結束索引，負值會轉換成 array_length + end。

注意：會忽略超出範圍的索引。

**傳回**

範圍 [`start..end`] 中值的動態陣列`arr`。

**範例**


```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1，2，3]|[2，3]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|分層|
|---|---|
|[1，2，3，4，5]|[3，4，5]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|分層|
|---|---|
|[1，2，3，4，5]|[3，4]|