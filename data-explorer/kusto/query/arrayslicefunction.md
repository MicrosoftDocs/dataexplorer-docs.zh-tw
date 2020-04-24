---
title: array_slice() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的array_slice()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ed5c320ef639f7545e19bcaabd9b53f4424c959d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518576"
---
# <a name="array_slice"></a>array_slice()

提取動態陣列的切片。

**語法**

`array_slice(`*arr,**開始*,*結束*|`)`

**引數**

* *arr*:輸入陣列要從中提取切片,必須是動態陣組。
* *開始*:切片的零基(包含)起始索引,負值轉換為array_length_start。
* *結束*:切片的零基(包含)結束索引,負值轉換為array_length_end。

注意:忽略超出邊界的索引。

**傳回**

範圍 [開始.結束*從arr。

**範例**

1.
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|阿爾爾|切片|
|---|---|
|[1,2,3]|[2,3]|


2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|阿爾爾|切片|
|---|---|
|[1,2,3,4,5]|[3,4,5]|


3.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|阿爾爾|切片|
|---|---|
|[1,2,3,4,5]|[3,4]|