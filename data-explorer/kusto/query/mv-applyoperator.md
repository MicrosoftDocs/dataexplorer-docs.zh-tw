---
title: mv-apply 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 mv-apply 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 656d1d5d9120baebced624fee476d261214eff57
ms.sourcegitcommit: 83202ec6fec0ce98fdf993bbb72adc985d6d9c78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87871845"
---
# <a name="mv-apply-operator"></a>mv-apply 運算子

將子查詢套用至每一筆記錄，並傳回所有子查詢結果的聯集。

例如，假設資料表 `T` 具有 `Metric` 類型的資料行， `dynamic` 其值為數字的陣列 `real` 。 下列查詢會找出每個值中的兩個最大值 `Metric` ，並傳回對應至這些值的記錄。

```kusto
T | mv-apply Metric to typeof(real) on 
(
   top 2 by Metric desc
)
```

`mv-apply`運算子具有下列處理步驟：

1. 使用 [`mv-expand`](./mvexpandoperator.md) 運算子，將輸入中的每一筆記錄展開為 subtables。
1. 針對每個 subtables 套用子查詢。
1. 將零個或多個資料行加入至產生的 subtable。 這些資料行包含未展開之來源資料行的值，而且會在需要時重複。
1. 傳回結果的聯集。

`mv-apply`運算子會取得下列輸入：

1. 一或多個評估為要展開之動態陣列的運算式。
   每個展開的 subtable 中的記錄數目是每個動態陣列的最大長度。 如果指定了多個運算式，而且對應的陣列有不同的長度，則會加入 Null 值。

1. （選擇性）要在展開後指派運算式值的名稱。
   這些名稱會成為 subtables 中的資料行名稱。
   如果未指定，則在運算式為數據行參考時，會使用資料行的原始名稱。 否則會使用隨機名稱。 

   > [!NOTE]
   > 建議使用預設的資料行名稱。

1. 在展開之後，這些動態陣列之專案的資料類型。
   這些會成為 subtables 中資料行的資料行類型。
   如果未指定，就會使用 `dynamic`。

1. （選擇性）要加入至 subtables 的資料行名稱，指定陣列中產生 subtable 記錄之元素的以零為起始的索引。

1. （選擇性）要展開之陣列元素的最大數目。

`mv-apply`運算子可視為運算子的一般化 [`mv-expand`](./mvexpandoperator.md) (事實上，如果子查詢只包含投影，後者就可以由前者來執行。 ) 

## <a name="syntax"></a>語法

*T* `|` `mv-apply` [*ItemIndex*] *ColumnsToExpand* [*RowLimit*] `on` `(` *子查詢*`)`

其中*ItemIndex*的語法如下：

`with_itemindex``=` *IndexColumnName*

*ColumnsToExpand*是以逗號分隔的清單，其中的一或多個元素格式如下：

[*名稱* `=` ]*產生陣列*[ `to` `typeof` `(` *Typename* `)` ]

*RowLimit*只是：

`limit`*RowLimit*

和*子查詢*具有與任何查詢語句相同的語法。

## <a name="arguments"></a>引數

* *ItemIndex*：如果使用，則指出類型的資料行名稱，此資料行會 `long` 附加至輸入做為陣列擴充階段的一部分，並指出擴充值的以0為基礎的陣列索引。

* *名稱*：如果使用，則為每個陣列展開的運算式指派陣列擴充值的名稱。
  如果未指定，則會使用資料行的名稱（如果有的話）。
  如果*產生陣列*不是簡單的資料行名稱，就會產生隨機名稱。

* *產生陣列*：類型的運算式， `dynamic` 其值將會被陣列擴充。
  如果運算式是輸入中的資料行名稱，則會從輸入中移除輸入資料行，而如果指定) 出現在輸出中，則 (或*ColumnName*的新資料行。

* *Typename*：如果使用，則為數組中個別元素所產生陣列的類型名稱 `dynamic` 。 *ArrayExpression* 不符合此類型的專案將會取代為 null 值。
   (如果未指定， `dynamic` 預設會使用。 ) 

* *RowLimit*：如果使用，則為每個輸入記錄所產生的記錄數目限制。
   (如果未指定，則會使用2147483647。 ) 

* *子查詢*：具有隱含表格式來源的表格式查詢運算式，會套用至每個陣列擴充的 subtable。

**注意事項**

* 不同于 [`mv-expand`](./mvexpandoperator.md) 運算子， `mv-apply` 運算子僅支援陣列擴充。 不支援擴充屬性包。

## <a name="examples"></a>範例

## <a name="getting-the-largest-element-from-the-array"></a>從陣列取得最大的元素

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply element=l to typeof(long) on 
(
   top 1 by element
)
```

|`xMod2`|l           |element|
|-----|------------|-------|
|1    |[1，3，5，7]|7      |
|0    |[2，4，6，8]|8      |

## <a name="calculating-the-sum-of-the-largest-two-elements-in-an-array"></a>計算陣列中最大兩個元素的總和

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply l to typeof(long) on
(
   top 2 by l
   | summarize SumOfTop2=sum(l)
)
```

|`xMod2`|l        |SumOfTop2|
|-----|---------|---------|
|1    |[1，3，5，7]|12       |
|0    |[2，4，6，8]|14       |


## <a name="using-with_itemindex-for-working-with-a-subset-of-the-array"></a>使用 `with_itemindex` 來處理陣列的子集

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 10 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply with_itemindex=index element=l to typeof(long) on 
(
   // here you have 'index' column
   where index >= 3
)
| project index, element
```

|索引|element|
|---|---|
|3|7|
|4|9|
|3|8|
|4|10|

## <a name="using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key"></a>使用 `mv-apply` 運算子排序 `makelist` 依某個索引鍵匯總的輸出

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',        datetime(2019-07-15),   "user1",
    'ls',           datetime(2019-07-02),   "user1",
    'dir',          datetime(2019-07-22),   "user1",
    'mkdir',        datetime(2019-07-14),   "user1",
    'rm',           datetime(2019-07-27),   "user1",
    'pwd',          datetime(2019-07-25),   "user1",
    'rm',           datetime(2019-07-23),   "user2",
    'pwd',          datetime(2019-07-25),   "user2",
]
| summarize commands_details = make_list(pack('command', command, 'command_time', command_time)) by user_id
| mv-apply command_details = commands_details on
(
    order by todatetime(command_details['command_time']) asc
    | summarize make_list(tostring(command_details['command']))
)
| project-away commands_details
```

|`user_id`|`list_command_details_command`|
|---|---|
|user1|[<br>  "ls"，<br>  "mkdir"，<br>  "chmod"，<br>  "dir"，<br>  "pwd"，<br>  rm<br>]|
|user2|[<br>  「rm」，<br>  pwd<br>]|


**另請參閱**

* [mv-展開](./mvexpandoperator.md)運算子。
