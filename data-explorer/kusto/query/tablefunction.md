---
title: table （）（範圍函數）-Azure 資料總管
description: 本文說明 Azure 資料總管中的資料表（）（範圍函數）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 0d5f44d621612e90d83a93f2f5831630520d4ba0
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83371741"
---
# <a name="table-scope-function"></a>table （）（scope 函數）

Table （）函數會藉由提供名稱做為類型的運算式，來參考資料表 `string` 。

```kusto
table('StormEvent')
```

**語法**

`table``(` *TableName* [ `,` *DataScope*]`)`

**引數**

::: zone pivot="azuredataexplorer"

* *TableName*：類型 `string` 的運算式，提供所參考之資料表的名稱。 這個運算式的值在函式的呼叫點必須是常數（也就是它不能因數據內容而異）。

* *DataScope*：類型的選擇性參數 `string` ，可用來根據此資料在資料表的有效快取[原則](../management/cachepolicy.md)下，限制資料的資料表參考。 如果使用，則實際引數必須是 `string` 具有下列其中一個可能值的常數運算式：

    - `"hotcache"`：只會參考分類為熱快取的資料。
    - `"all"`：將會參考資料表中的所有資料。
    - `"default"`：這與相同 `"all"` ，除非叢集已設定為使用叢集 `"hotcache"` 管理員的預設值。

::: zone-end

::: zone pivot="azuremonitor"

* *TableName*：類型 `string` 的運算式，提供所參考之資料表的名稱。 這個運算式的值在函式的呼叫點必須是常數（也就是它不能因數據內容而異）。

* *DataScope*：類型的選擇性參數 `string` ，可用來根據此資料在資料表的有效快取原則下，限制資料的資料表參考。 如果使用，則實際引數必須是 `string` 具有下列其中一個可能值的常數運算式：

    - `"hotcache"`：只會參考分類為熱快取的資料。
    - `"all"`：將會參考資料表中的所有資料。
    - `"default"`：這與相同 `"all"` 。

::: zone-end

## <a name="examples"></a>範例

### <a name="use-table-to-access-table-of-the-current-database"></a>使用 table （）存取目前資料庫的資料表

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
table('StormEvent') | count
```

|計數|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>使用 let 語句中的 table （）

與上述相同的查詢可以重寫成使用內嵌函式（let 語句）來接收參數（ `tableName` 傳遞至 table （）函數）。

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

### <a name="use-table-inside-functions"></a>在函數中使用 table （）

您可以重新撰寫與上述相同的查詢，以在接收參數的函式中使用，該函數 `tableName` 會傳遞至 table （）函數。

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**注意：** 這類函式只能在本機使用，而不能用於跨叢集查詢。

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>搭配使用 table （）與非常數參數

不能將不是純量常數位串的參數當做參數傳遞給函式 `table()` 。

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
