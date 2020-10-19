---
title: 'array_sort_desc ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 array_sort_desc。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 09/22/2020
ms.openlocfilehash: 83be80a7eb440e73fd19f213839cef7d58ec1512
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169624"
---
# <a name="array_sort_desc"></a>array_sort_desc ( # A1

接收一或多個陣列。 以遞減順序排序第一個陣列。 排序其餘的陣列，使其符合重新排序的第一個陣列。

## <a name="syntax"></a>Syntax

`array_sort_desc(`*array1*[，...， *argumentN*]`)`

`array_sort_desc(`*array1*[，...， *argumentN*] `,` *nulls_last*`)`

如果未提供 *nulls_last* ， `true` 則會使用預設值。

## <a name="arguments"></a>引數

* *array1 .。。arrayN*：輸入陣列。
* *nulls_last*：布林值，指出是否 `null` 應為最後一次

## <a name="returns"></a>傳回

傳回與輸入中相同數目的陣列，第一個陣列以遞增順序排序，其餘的陣列則排序以符合重新排序的第一個陣列。

`null` 將會針對每個不同于第一個陣列的長度傳回。

如果陣列包含不同類型的元素，則會依下列順序排序：

* Numeric、 `datetime` 和 `timespan` 元素
* 字串元素
* Guid 元素
* 所有其他元素

## <a name="example-1---sorting-two-arrays"></a>範例 1-排序兩個數組

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let array1 = dynamic([1,3,4,5,2]);
let array2 = dynamic(["a","b","c","d","e"]);
print array_sort_desc(array1,array2)
```

|`array1_sorted`|`array2_sorted`|
|---|---|
|[5，4，3，2，1]|["d"、"c"、"b"、"e"、"a"]|

> [!Note]
> 輸出資料行名稱會根據函數的引數自動產生。 若要將不同的名稱指派給輸出資料行，請使用下列語法： `... | extend (out1, out2) = array_sort_desc(array1,array2)`

## <a name="example-2---sorting-substrings"></a>範例 2-排序子字串

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Names = "John,Paul,George,Ringo";
let SortedNames = strcat_array(array_sort_desc(split(Names, ",")), ",");
print result = SortedNames
```

|`result`|
|---|
|Ringo、Paul、John、George|

## <a name="example-3---combining-summarize-and-array_sort_desc"></a>範例 3-合併摘要和 array_sort_desc

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',   datetime(2019-07-15),   "user1",
    'ls',      datetime(2019-07-02),   "user1",
    'dir',     datetime(2019-07-22),   "user1",
    'mkdir',   datetime(2019-07-14),   "user1",
    'rm',      datetime(2019-07-27),   "user1",
    'pwd',     datetime(2019-07-25),   "user1",
    'rm',      datetime(2019-07-23),   "user2",
    'pwd',     datetime(2019-07-25),   "user2",
]
| summarize timestamps = make_list(command_time), commands = make_list(command) by user_id
| project user_id, commands_in_chronological_order = array_sort_desc(timestamps, commands)[1]
```

|`user_id`|`commands_in_chronological_order`|
|---|---|
|user1|[<br>  「rm」、<br>  "pwd"、<br>  "dir"、<br>  "chmod"、<br>  "mkdir"、<br>  3<br>]|
|user2|[<br>  "pwd"、<br>  ws-rm<br>]|

> [!Note]
> 如果您的資料可能包含 `null` 值，請使用 [make_list_with_nulls](make-list-with-nulls-aggfunction.md) 而不是 [make_list](makelist-aggfunction.md)。

## <a name="example-4---controlling-location-of-null-values"></a>範例 4-控制值的位置 `null`

依預設， `null` 值會放在已排序陣列中的最後一個。 不過，您可以藉由將 `bool` 值做為的最後一個引數來明確控制 `array_sort_desc()` 。

預設行為的範例：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]))
```

|`print_0`|
|---|
|["黃色"、"綠"、"blue"、null、null]|

使用非預設行為的範例：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]), false)
```

|`print_0`|
|---|
|[null、null、"黃"、"綠"、"blue"]|

## <a name="see-also"></a>另請參閱

若要以遞增順序排序第一個陣列，請使用 [array_sort_asc ( # B1 ](arraysortascfunction.md)。
