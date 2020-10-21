---
title: '資料表 ( # A1 (範圍函數) -Azure 資料總管'
description: '本文說明 Azure 資料總管中的資料表 ( # A1 (範圍函數) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 80035e20110b8a1f2fb705ef73f75275f60fcdda
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251304"
---
# <a name="table-scope-function"></a>資料表 ( # A1 (範圍函數) 

資料表 ( # A1 函數會將其名稱提供為類型的運算式，以參考資料表 `string` 。

```kusto
table('StormEvent')
```

## <a name="syntax"></a>語法

`table``(` *TableName* [ `,` *DataScope*]`)`

## <a name="arguments"></a>引數

::: zone pivot="azuredataexplorer"

* *TableName*： `string` 提供所參考資料表名稱的型別運算式。 此運算式的值在函式呼叫時必須是常數 (也就是說，它不會因數據內容) 而異。

* *DataScope*：型別的選擇性參數 `string` ，可用來根據此資料在資料表的有效快取 [原則](../management/cachepolicy.md)下，限制資料的資料表參考。 如果使用，則實際的引數必須是 `string` 具有下列其中一個可能值的常數運算式：

    - `"hotcache"`：只會參考分類為熱快取的資料。
    - `"all"`：將會參考資料表中的所有資料。
    - `"default"`：這與相同 `"all"` ，但叢集系統管理員已將叢集設定為使用 `"hotcache"` 預設值。

::: zone-end

::: zone pivot="azuremonitor"

* *TableName*： `string` 提供所參考資料表名稱的型別運算式。 此運算式的值在函式呼叫時必須是常數 (也就是說，它不會因數據內容) 而異。

* *DataScope*：型別的選擇性參數 `string` ，可用來根據此資料在資料表的有效快取原則下，限制資料的資料表參考。 如果使用，則實際的引數必須是 `string` 具有下列其中一個可能值的常數運算式：

    - `"hotcache"`：只會參考分類為熱快取的資料。
    - `"all"`：將會參考資料表中的所有資料。
    - `"default"`：這與相同 `"all"` 。

::: zone-end

## <a name="examples"></a>範例

### <a name="use-table-to-access-table-of-the-current-database"></a>使用資料表 ( # A1 來存取目前資料庫的資料表

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
table('StormEvent') | count
```

|計數|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>在 let 語句內使用資料表 ( # A1

您可以重寫上述相同的查詢，以使用內嵌函式 (let 語句) 來接收參數， `tableName` 該參數會傳遞至資料表 ( # A3 函數。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo = (tableName:string)
{
    table(tableName) | count
};
foo('help')
```

|計數|
|---|
|59066|

### <a name="use-table-inside-functions"></a>在函式中使用資料表 ( # A1

您可以重寫上述相同的查詢，以在接收參數的函式中使用， `tableName` 該函式會傳遞至資料表 ( # A1 函數。

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**注意：** 這類函數只能用在本機，而不能在跨叢集查詢中使用。

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>使用具有非常數參數的資料表 ( # A1

非純量常數位符串的參數無法以參數形式傳遞給函式 `table()` 。

以下提供此案例的因應措施範例。

```kusto
let T1 = print x=1;
let T2 = print x=2;
let _choose = (_selector:string)
{
    union
    (T1 | where _selector == 'T1'),
    (T2 | where _selector == 'T2')
};
_choose('T2')

```

|x|
|---|
|2|
