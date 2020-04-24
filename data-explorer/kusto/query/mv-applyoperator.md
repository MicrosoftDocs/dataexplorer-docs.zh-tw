---
title: mv 應用運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 mv 應用運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f24bf7721707aa1ba3ae9f0aad49b247f08c2498
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512303"
---
# <a name="mv-apply-operator"></a>mv-apply 運算子

mv-apply 運算子將輸入表中的每個記錄展開到子表中,將子查詢應用於每個子表,並返回所有子查詢結果的合併。

例如,假設`T`表具有一個類型`Metric``dynamic`的列,其值是數位陣組`real`。 以下查詢將查找每個`Metric`值中的兩個最大值,並返回與這些值對應的記錄。

```kusto
T | mv-apply Metric to typeof(real) on (top 2 by Metric desc)
```

通常,mv-apply 運算元可以被視為具有以下處理步驟:

1. 它使用[mv-expand](./mvexpandoperator.md)運算符將輸入中的每個記錄擴展到子表中。
2. 它為每個子表應用子查詢。
3. 它為每個生成的子表預置零列或更多列,其中包含未展開的源列的(如有必要重複)值。
4. 它返回結果的合併。

mv-展開運算子抓取以下輸入:

1. 一個或多個表達式,這些表達式計算為動態數位以展開。
   每個擴展子表中的記錄數是每個動態數位的最大長度。 (如果指定了多個表達式,但相應的陣列長度不同,則在必要時引入空值。

2. 可選,用於在擴展後分配表達式值的名稱。
   這些將成為子表中列的名稱。
   如果未指定,則使用列的原始名稱(如果表達式是列引用),或者使用隨機名稱(否則)。

   > [!NOTE]
   > 建議使用預設列名稱。

3. 擴展後這些動態數位元素的數據類型。
   這些將成為子表中列的列類型。
   如果未指定，就會使用 `dynamic`。

4. 或者,要添加到子表的列的名稱,該子表指定導致子表記錄的陣列中元素的 0 的索引。

5. 可選,要展開的陣列元素的最大數量。

mv-apply 運算符可以被視為[mv-expand](./mvexpandoperator.md)運算符的概括(事實上,如果子查詢僅包含投影,則後者可以由前者實現。

**語法**

*T* `mv-apply` `on``(`*RowLimit**SubQuery* *ColumnsToExpand**ItemIndex*T = 項目索引 = 要展開 的欄位 : 列限制 = 子查詢`|``)`

*其中 ItemIndex*具有語法:

`with_itemindex``=`*索引欄名稱*

*列ToExpand*是表單的一個或多個元素的逗號分隔清單:

【*名稱*`=`】*陣列表示式*`to``typeof``(`[*類型名稱*`)`】

*RowLimit 限制*很簡單:

`limit`*行限制*

和*子查詢*具有與任何查詢語句相同的語法。

**引數**

* *ItemIndex*:如果使用,則指示作為陣列擴展階段的一`long`部分 追加到輸入中的類型的列的名稱,並指示展開值的基於 0 的陣列索引。

* *名稱*:如果使用,則指定每個陣列展開表達式的數位展開值的名稱。
  (如果未指定,將使用列的名稱(如果可用),或者如果*ArrayExpression*不是簡單的列名稱,則生成隨機名稱。

* *陣列表示式*`dynamic`: 其值將陣列展開的類型運算式。
  如果表示式是輸入中的列的名稱,則輸入列將從輸入中刪除,輸出中將顯示同名的新列(如果指定則*為列名稱*)。

* *類型名稱*:如果使用`dynamic`, 則陣列*式陣式表示式*的各個元素採用的類型的名稱。 不符合此類型的元素將被替換為 null 值。
  (如果未指定,`dynamic`則預設使用。

* *RowLimit*:如果使用,則限制從輸入的每個記錄生成的記錄數。
  (如果未指定,則使用 2147483647。

* *子查詢*:具有隱式表式源的表格查詢表達式,該運算式應用於每個陣列展開的子表。

**注意事項**

* 與[mv-展開](./mvexpandoperator.md)運算符不同,mv-apply 運算符僅支援陣列擴展。 沒有支持擴大財產袋。

**範例**

## <a name="getting-the-largest-element-from-the-array"></a>從陣列取得最大的元素

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

|xMod2|l           |element|
|-----|------------|-------|
|1    |[1, 3, 5, 7]|7      |
|0    |[2, 4, 6, 8]|8      |

## <a name="calculating-sum-of-largest-two-elments-in-an-array"></a>計算陣列最大兩個 els 的求和

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

|xMod2|l        |SumofTop2|
|-----|---------|---------|
|1    |[1,3,5,7]|12       |
|0    |[2,4,6,8]|14       |


## <a name="using-with_itemindex-for-working-with-subset-of-the-array"></a>處理`with_itemindex`陣列的子集

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

## <a name="using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key"></a>使用`mv-apply`運算子按某些鍵對聚合`makelist`輸出進行排序

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

|user_id|list_command_details_command|
|---|---|
|user1|[<br>  "ls"<br>  "mkdir"<br>  "chmod"<br>  "迪爾"<br>  "pwd",<br>  "rm"<br>]|
|user2|[<br>  "rm",<br>  "pwd"<br>]|


**另請參閱**

* [mv-展開](./mvexpandoperator.md)運算符。