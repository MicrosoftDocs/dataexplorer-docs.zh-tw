---
title: 'array_rotate_right ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 array_rotate_right。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: c74dd0aacd4360e601ec58c6a767debadcbc2d05
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249422"
---
# <a name="array_rotate_right"></a>array_rotate_right()

將陣列中的值旋轉 `dynamic` 到右邊。

## <a name="syntax"></a>語法

`array_rotate_right(`*arr*、 *rotate_count*`)`

## <a name="arguments"></a>引數

* *arr*：要分割的輸入陣列，必須是動態陣列。
* *rotate_count*：整數，指定陣列元素將旋轉到右邊的位置數目。 如果值為負數，則會將元素向左旋轉。

## <a name="returns"></a>傳回

動態陣列，包含與原始陣列中相同數量的元素，其中每個專案都會根據 *rotate_count*旋轉。

## <a name="see-also"></a>另請參閱

* 若要將陣列向左旋轉，請參閱 [array_rotate_left ( # B1 ](array_rotate_leftfunction.md)。
* 若要將陣列向左移位，請參閱 [array_shift_left ( # B1 ](array_shift_leftfunction.md)。
* 若要將陣列向右移位，請參閱 [array_shift_right ( # B1 ](array_shift_rightfunction.md)。

## <a name="examples"></a>範例

* 由兩個位置向右旋轉：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, 2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1，2，3，4，5]|[4，5，1，2，3]|

* 使用負 rotate_count 值，在兩個位置左右旋轉：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, -2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1，2，3，4，5]|[3，4，5，1，2]|
