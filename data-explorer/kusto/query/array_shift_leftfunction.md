---
title: array_shift_left() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的array_shift_left()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 5b623bdcccc75261d0fb9324bb6015e16b0ff9b8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518780"
---
# <a name="array_shift_left"></a>array_shift_left()

`array_shift_left()`將數位內部的值向左移動。

**語法**

`array_shift_left(`*阿爾,* *shift_count* [, *fill_value* ]`)`

**引數**

* *arr*:輸入陣列要拆分,必須是動態陣組。
* *shift_count*: 整數指定陣列元素將向左移動的位置數。 如果值為負值,則元素將向右移動。
* *fill_value*: 用於插入元素的 Scalar 值,而不是已移動和移除的元素。 預設值:空值或空字串(取決於*arr*類型)。

**傳回**

包含與原始陣列中的元素量相同的動態數位,其中每個元素都根據*shift_count*移動。 新增的元素而非刪除的元素的值會為*fill_value*。

**另請參閱**

* 有關向右移動陣列,請參閱[array_shift_right()](array_shift_rightfunction.md)。
* 有關右旋轉陣列,請參閱[array_rotate_right()](array_rotate_rightfunction.md)。
* 有關左側旋轉陣列,請參閱[array_rotate_left()](array_rotate_leftfunction.md)。

**範例**

* 由兩個位置向左移動:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2)
    ```
    
    |阿爾爾|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,null,null]|

* 由兩個位置向左移動並新增預設值:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2, -1)
    ```
    
    |阿爾爾|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,-1,-1]|


* 使用負*shift_count*值向右移動兩個位置:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, -2, -1)
    ```
    
    |阿爾爾|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[-1,-1,1,2,3]|
