---
title: 表() (range) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的表(範圍函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 233baebaf51cdc8b07cdd32cec9bd10a54695c1c
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766148"
---
# <a name="table-scope-function"></a>表() (range)

表() 函數通過提供表的名稱作為類型`string`的 運算式來引用表。

```kusto
table('StormEvent')
```

**語法**

`table``(`*表名*`,`(*資料範圍*)`)`

**引數**

::: zone pivot="azuredataexplorer"

* *表名稱*:提供被`string`引用 表名稱的類型運算式。 此表達式的值在調用函數時必須為常量(即它不能因數據上下文而異)。

* *DataScope:* 一種可選類型`string`的 參數,可用於根據數據在表的有效[快取策略](../management/cachepolicy.md)下如何限制表引用資料。 如果使用,實際參數必須是具有以下可能值之`string`一的常量運算式:

    - `"hotcache"`:將僅引用分類為熱緩存的數據。
    - `"all"`: 將引用表中的所有資料。
    - `"default"`:這與`"all"`相同 ,除非群集管理員將群集設置為預設`"hotcache"`使用。

::: zone-end

::: zone pivot="azuremonitor"

* *表名稱*:提供被`string`引用 表名稱的類型運算式。 此表達式的值在調用函數時必須為常量(即它不能因數據上下文而異)。

* *DataScope*:一種可選類型`string`的 參數,可用於根據數據在表的有效緩存策略下如何限制表引用數據。 如果使用,實際參數必須是具有以下可能值之`string`一的常量運算式:

    - `"hotcache"`:將僅引用分類為熱緩存的數據。
    - `"all"`: 將引用表中的所有資料。
    - `"default"`: 這`"all"`與 相同。

::: zone-end

## <a name="examples"></a>範例

### <a name="use-table-to-access-table-of-the-current-database"></a>使用表格() 存取目前資料庫的表

```kusto
table('StormEvent') | count
```

|Count|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>在 let 語片內使用表()

可以重寫與上述相同的查詢,以使用接收參數的內聯函數(let 語句),該函`tableName`數將傳遞到表()函數中。

```kusto
let foo = (tableName:string)
{
    table(tableName) | count
};
foo('help')
```

|Count|
|---|
|59066|

### <a name="use-table-inside-functions"></a>在函數內使用表()

可以重寫與上述相同的查詢,以用於接收參數的函數 -`tableName`該參數 傳遞到 table() 函數中。

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**注意:** 此類函數只能在本地使用,不能在跨群集查詢中使用。

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>將表()與非常量參數一起使用

參數,它不是標量常量字串不能作為參數傳遞`table()`來運行。

下面,給出了此類案例的解決方法示例。

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
