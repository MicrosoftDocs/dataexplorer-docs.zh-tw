---
title: 'array_shift_left ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 array_shift_left。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 4bdadd276a59b30ed347a3e293eb5e5c7831063b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247030"
---
# <a name="array_shift_left"></a>array_shift_left()

將陣列中的值 `dynamic` 向左移動。

## <a name="syntax"></a>語法

`array_shift_left(`*`arr`*, *`shift_count`* `[,` *`fill_value`* ]`)`

## <a name="arguments"></a>引數

* *`arr`*：要分割的輸入陣列，必須是動態陣列。
* *`shift_count`*：指定陣列元素要向左移位的位置數目的整數。 如果值為負數，則會將元素向右移動。
* *`fill_value`*：用來插入專案的純量值，而非移位和移除的專案。 預設值： null 值或空字串 (視 *`arr`*) 類型而定。

## <a name="returns"></a>傳回

動態陣列，包含與原始陣列中相同數量的元素。 每個元素都已根據 *shift_count*來移動。 為了取代移除的元素而加入的新專案，將會有 *fill_value*的值。

## <a name="see-also"></a>另請參閱

* 如需右移陣列，請參閱 [array_shift_right ( # B1 ](array_shift_rightfunction.md)。
* 如需輪替陣列許可權，請參閱 [array_rotate_right ( # B1 ](array_rotate_rightfunction.md)。
* 若為左方旋轉陣列，請參閱 [array_rotate_left ( # B1 ](array_rotate_leftfunction.md)。

## <a name="examples"></a>範例

* 由兩個位置向左移位：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1，2，3，4，5]|[3，4，5，null，null]|

* 依兩個位置向左移，並新增預設值：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1，2，3，4，5]|[3，4，5，-1，-1]|


* 使用負 *shift_count* 值，在兩個位置右移：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, -2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1，2，3，4，5]|[-1，-1，1，2，3]|
