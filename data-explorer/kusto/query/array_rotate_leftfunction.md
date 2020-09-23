---
title: 'array_rotate_left ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 array_rotate_left。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: a7f434f2425815f154ffda701832fa5e3ee70f05
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102819"
---
# <a name="array_rotate_left"></a>array_rotate_left()

`array_rotate_left()` 將陣列中的值旋轉至左方。

## <a name="syntax"></a>語法

`array_rotate_left(`*arr*、 *rotate_count*`)`

## <a name="arguments"></a>引數

* *arr*：要分割的輸入陣列，必須是動態陣列。
* *rotate_count*：整數，指定陣列元素將旋轉至左方的位置數目。 如果值為負數，則會將元素向右旋轉。

## <a name="returns"></a>傳回

動態陣列，包含與原始陣列中相同數量的元素，其中每個專案都會根據 *rotate_count*旋轉。

## <a name="see-also"></a>另請參閱

* 若要將陣列向右旋轉，請參閱 [array_rotate_right ( # B1 ](array_rotate_rightfunction.md)。
* 若要將陣列向左移位，請參閱 [array_shift_left ( # B1 ](array_shift_leftfunction.md)。
* 若要將陣列向右移位，請參閱 [array_shift_right ( # B1 ](array_shift_rightfunction.md)。

## <a name="examples"></a>範例

* 向左旋轉兩個位置：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1，2，3，4，5]|[3，4，5，1，2]|

* 使用負 rotate_count 值，在兩個位置向右旋轉：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1，2，3，4，5]|[4，5，1，2，3]|