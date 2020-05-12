---
title: array_rotate_left （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 array_rotate_left （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: ae28848ee46a4313ac2f24fb8a796cd0ced3ba4d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225814"
---
# <a name="array_rotate_left"></a>array_rotate_left()

`array_rotate_left()`將陣列中的值向左旋轉。

**語法**

`array_rotate_left(`*arr*、 *rotate_count*`)`

**引數**

* *arr*：要分割的輸入陣列，必須是動態陣列。
* *rotate_count*：指定陣列元素將會旋轉到左邊的位置數目的整數。 如果值為負數，則會將元素旋轉至右側。

**傳回**

動態陣列，包含與原始陣列相同的專案數量，其中每個元素都是根據*rotate_count*旋轉。

**另請參閱**

* 如需將陣列旋轉至右側，請參閱[array_rotate_right （）](array_rotate_rightfunction.md)。
* 針對向左移位的陣列，請參閱[array_shift_left （）](array_shift_leftfunction.md)。
* 如需將陣列向右移位，請參閱[array_shift_right （）](array_shift_rightfunction.md)。

**範例**

* 向左旋轉兩個位置：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1，2，3，4，5]|[3，4，5，1，2]|

* 使用負數 rotate_count 值，在兩個位置向右旋轉：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1，2，3，4，5]|[4，5，1，2，3]|