---
title: 'array_shift_right ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 array_shift_right。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: af983f63046280d4ddd237107d1d2cf35d6cce2d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246992"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()` 將陣列中的值右移至右邊。

## <a name="syntax"></a>語法

`array_shift_right(`*`arr`*, *`shift_count`* [, *`fill_value`* ]`)`

## <a name="arguments"></a>引數

* *`arr`*：要分割的輸入陣列，必須是動態陣列。
* *`shift_count`*：整數，指定陣列元素將右移至右邊的位置數目。 如果值為負數，則會將元素向左移位。
* *`fill_value`*：用來插入專案的純量值，而非移位和移除的專案。 預設值： null 值或空字串 (視 *arr* 類型) 而定。

## <a name="returns"></a>傳回

動態陣列，包含與原始陣列中相同數量的元素。 每個元素都已根據來移動 *`shift_count`* 。 新增的專案（而非移除的元素）的值將會是 *`fill_value`* 。

## <a name="see-also"></a>另請參閱

* 若為左方陣列，請參閱 [array_shift_left ( # B1 ](array_shift_leftfunction.md)。
* 如需輪替陣列許可權，請參閱 [array_rotate_right ( # B1 ](array_rotate_rightfunction.md)。
* 若為左方旋轉陣列，請參閱 [array_rotate_left ( # B1 ](array_rotate_leftfunction.md)。

## <a name="examples"></a>範例

* 由兩個位置向右移位：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1，2，3，4，5]|[null，null，1，2，3]|

* 由兩個位置向右移，並新增預設值：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1，2，3，4，5]|[-1，-1，1，2，3]|

* 使用負 shift_count 值，以兩個位置向左移位：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1，2，3，4，5]|[3，4，5，-1，-1]|
    