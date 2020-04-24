---
title: array_split() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 array_split()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 4d5d8ce5e918f335f1f26f4e3fc045ab0fa6e3a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518559"
---
# <a name="array_split"></a>array_split()

根據拆分索引將數位拆分為多個陣列,並將生成的陣列打包到動態陣列中。

**語法**

`array_split(`*arr*,*指數*`)`

**引數**

* *arr*:輸入陣列要拆分,必須是動態陣組。
* *索引*: 整數或帶分割索引(基於零)的整數或動態陣列,負值轉換為array_length = 值。

**傳回**

包含 N+1 陣列的動態陣列,其值在範圍 [0...i1] 中,[i1.]i2),...[iN.array_length)來自 arr,其中 N 是輸入索引和 i1.iN 是索引。

**範例**

1.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```
|阿爾爾|arr_split|
|---|---|
|[1,2,3,4,5]|[[1,2],[3,4,5]]|



2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```
|阿爾爾|arr_split|
|---|---|
|[1,2,3,4,5]|[[1],[2,3],[4,5]]|