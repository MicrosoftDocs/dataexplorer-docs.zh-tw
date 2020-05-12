---
title: array_shift_right （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 array_shift_right （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 714c6c15443420abbc973593acb2f311a5507dc4
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225661"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()`將陣列內的值向右移位。

**語法**

`array_shift_right(`*arr*， *shift_count* [， *fill_value* ]`)`

**引數**

* *arr*：要分割的輸入陣列，必須是動態陣列。
* *shift_count*：指定陣列元素要向右移動的位置數目的整數。 如果值為負數，則會將元素向左移動。
* *fill_value*：用來插入元素的純量值，而不是已移動和移除的專案。 Default： null 值或空字串（視*arr*類型而定）。

**傳回**

動態陣列，包含與原始陣列中相同數量的元素，其中每個專案都是根據*shift_count*來移位。 新增的新專案（而不是移除的元素）將具有*fill_value*的值。

**另請參閱**

* 如需將陣列向左移動，請參閱[array_shift_left （）](array_shift_leftfunction.md)。
* 如需旋轉陣列許可權，請參閱[array_rotate_right （）](array_rotate_rightfunction.md)。
* 如需將陣列旋轉，請參閱[array_rotate_left （）](array_rotate_leftfunction.md)。

**範例**

* 由兩個位置向右移位：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1，2，3，4，5]|[null，null，1，2，3]|

* 由兩個位置向右移位，並加入預設值：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1，2，3，4，5]|[-1，-1，1，2，3]|


* 使用負數 shift_count 值，向左移位兩個位置：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1，2，3，4，5]|[3，4，5，-1，-1]|
    