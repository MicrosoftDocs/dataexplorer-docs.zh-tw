---
title: array_rotate_left() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的array_rotate_left()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 82eb8c24a3ed04146e5416b020bda7085426d138
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518814"
---
# <a name="array_rotate_left"></a>array_rotate_left()

`array_rotate_left()`將陣列內的值向左旋轉。

**語法**

`array_rotate_left(`*阿爾 ,* *rotate_count*`)`

**引數**

* *arr*:輸入陣列要拆分,必須是動態陣組。
* *rotate_count*: 整數指定陣列元素將向左旋轉的位置數。 如果值為負值,則元素將向右旋轉。

**傳回**

包含與原始陣列中的元素量相同的動態數位,其中每個元素都根據*rotate_count*旋轉。

**另請參閱**

* 有關右側的旋轉陣列,請參閱[array_rotate_right()](array_rotate_rightfunction.md)。
* 有關向左移動陣列,請參閱[array_shift_left()](array_shift_leftfunction.md)。
* 有關向右移動陣列,請參閱[array_shift_right()](array_shift_rightfunction.md)。

**範例**

* 由兩個位置向左旋轉:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |阿爾爾|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,1,2]|

* 使用負rotate_count值向右旋轉兩個位置:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |阿爾爾|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[4,5,1,2,3]|