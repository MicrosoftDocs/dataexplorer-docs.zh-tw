---
title: 聯集運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的聯集運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6dd305ccf5e260666072a4ab2b0d8b6707894902
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339488"
---
# <a name="union-operator"></a>union 運算子

取得兩個或以上的資料表並傳回這些資料表中的資料列。 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>語法

*T* `| union` [*UnionParameters*] [ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ]*資料表*[ `,` *資料表*] .。。  

不含輸送輸入的替代表單：

`union`[*UnionParameters*][ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ]*資料表*[ `,` *資料表*] .。。  

## <a name="arguments"></a>引數

::: zone pivot="azuredataexplorer"

* `Table`:
    *  資料表名稱，例如 `Events`；或
    *  必須以括弧括住的查詢運算式，例如 `(Events | where id==42)` 或 `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))` ; 或
    *  使用萬用字元指定的一組資料表。 例如， `E*` 會形成名稱開始之資料庫中所有資料表的聯集 `E` 。
* `kind`: 
    * `inner` - 結果中會有所有輸入資料表共有的資料行子集。
    * `outer`-（預設值）。 結果會有任何輸入中出現的所有資料行。 輸入資料列未定義的資料格會設為 `null` 。
* `withsource`=*Columnname*：如果指定，輸出會包含一個名為*ColumnName*的資料行，其值會指出哪一個來源資料表已貢獻每個資料列。
如果查詢有效（在萬用字元比對之後）參考多個資料庫中的資料表（預設資料庫一律計算），則此資料行的值將具有以資料庫限定的資料表名稱。
同樣地，如果參考一個以上的叢集，值中就會出現叢集__和資料庫__的合格限制。 
* `isfuzzy=``true`  |  `false` ：如果 `isfuzzy` 設為，則允許對等 `true` 位邊緣進行模糊解析。 `Fuzzy`適用于 `union` 來源集。 這表示在分析查詢並準備執行時，會將一組聯集來源縮減為存在且可在當時存取的資料表參考集。 如果至少找到一個這類資料表，任何解決錯誤都會在查詢狀態結果中產生警告（每個遺漏參考各一個），但不會防止查詢執行;如果沒有成功的解決方式，查詢將會傳回錯誤。
預設值為 `isfuzzy=` `false`。
* *UnionParameters*：*名稱*值格式為零或多個（以空格分隔）的參數 `=` *Value* ，可控制資料列比對作業和執行計畫的行為。 支援下列參數： 

  |名稱           |值                                        |描述                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Number*|提示系統 `union` 應該以平行方式執行運算子的多個並行子查詢。 *預設值*：叢集的單一節點上的 CPU 核心數量（2到16個）。|
  |`hint.spread`|*Number*|提示系統並行子查詢執行應使用的節點數目 `union` 。 *預設值*：1。|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  資料表的名稱，例如`Events`
    *  必須以括弧括住的查詢運算式，例如`(Events | where id==42)`
    *  使用萬用字元指定的一組資料表。 例如， `E*` 會形成名稱開始之資料庫中所有資料表的聯集 `E` 。
* `kind`: 
    * `inner` - 結果中會有所有輸入資料表共有的資料行子集。
    * `outer`-（預設值）。 結果會有任何輸入中出現的所有資料行。 輸入資料列未定義的資料格會設為 `null` 。
* `withsource`=*Columnname*：如果指定，輸出會包含一個名為*ColumnName*的資料行，其值會指出每個資料列所提供的來源資料表。
如果查詢有效（在萬用字元比對之後）參考多個資料庫中的資料表（預設資料庫一律計算），則此資料行的值將具有以資料庫限定的資料表名稱。
同樣地，如果參考多個叢集，則值中將會出現叢集__和資料庫__的資格。 
* `isfuzzy=``true`  |  `false` ：如果 `isfuzzy` 設為，則允許對等 `true` 位邊緣進行模糊解析。 `Fuzzy`適用于 `union` 來源集。 這表示在分析查詢並準備執行時，會將一組聯集來源縮減為存在且可在當時存取的資料表參考集。 如果至少找到一個這類資料表，任何解決錯誤都會在查詢狀態結果中產生警告（每個遺漏參考各一個），但不會防止查詢執行;如果沒有成功的解決方式，查詢將會傳回錯誤。
預設值為 `isfuzzy=false`。

::: zone-end

## <a name="returns"></a>傳回

所含資料列數目和所有輸入資料表中的資料列數目一樣多的資料表。

**備註**

::: zone pivot="azuredataexplorer"

1. `union`範圍可以包含[let 語句](./letstatement.md)（如果它們是使用[view 關鍵字](./letstatement.md)進行屬性化）
2. `union`範圍不會包含[函數](../management/functions.md)。 若要在聯集範圍中包含函式，請使用[view 關鍵字](./letstatement.md)定義[let 語句](./letstatement.md)
3. 如果 `union` 輸入是[資料表](../management/tables.md)（如相比為[表格式運算式](./tabularexpressionstatements.md)），且 `union` 後面接著[where 運算子](./whereoperator.md)，則請考慮以[find](./findoperator.md)取代這兩個。 請注意運算子所產生的不同[輸出架構](./findoperator.md#output-schema) `find` 。 
4. `isfuzzy=true`僅適用于 `union` 來源解析階段。 一旦決定來源資料表的集合之後，就不會隱藏可能的其他查詢失敗。
5. 使用時 `outer union` ，結果會有所有輸入中出現的所有資料行，每個名稱和類型出現的一個資料行。 這表示如果資料行出現在多個資料表中，而且有多個類型，則在結果中，每個類型都會有對應的資料行 `union` 。 此資料行名稱後置字元會加上 ' _ '，後面接著來源資料行[類型](./scalar-data-types/index.md)。

::: zone-end

::: zone pivot="azuremonitor"

1. `union`範圍可以包含[let 語句](./letstatement.md)（如果它們是使用[view 關鍵字](./letstatement.md)進行屬性化）
2. `union`範圍不會包含函數。 若要在聯集範圍中包含函式-使用[view 關鍵字](./letstatement.md)定義[let 語句](./letstatement.md)
3. 如果 `union` 輸入是資料表（如相比為[表格式運算式](./tabularexpressionstatements.md)），且 `union` 後面接著[where 運算子](./whereoperator.md)，請考慮以「[尋找](./findoperator.md)」取代這兩個，以獲得更好的效能。 請注意運算子所產生的不同[輸出架構](./findoperator.md#output-schema) `find` 。 
4. `isfuzzy=``true`僅適用于來源解析的階段 `union` 。 一旦決定來源資料表的集合之後，就不會隱藏可能的其他查詢失敗。
5. 使用時 `outer union` ，結果會有所有輸入中出現的所有資料行，每個名稱和類型出現的一個資料行。 這表示如果資料行出現在多個資料表中，而且有多個類型，則在結果中，每個類型都會有對應的資料行 `union` 。 此資料行名稱後置字元會加上 ' _ '，後面接著來源資料行[類型](./scalar-data-types/index.md)。

::: zone-end


## <a name="example"></a>範例

```kusto
union K* | where * has "Kusto"
```

資料庫中的所有資料表中，名稱開頭為的資料 `K` 列，以及其中任何資料行包含該單字的 `Kusto` 。

## <a name="example"></a>範例

```kusto
union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```

過去一天已產生 `Query` 事件或 `Command` 事件的不同使用者數目。 在結果中，'SourceTable' 資料行會指出 "Query" 或 "Command"。

```kusto
Query
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

這個更有效率的版本會產生相同的結果。 它會先篩選每個資料表再建立聯集。

**範例：使用`isfuzzy=true`**
 
```kusto     
// Using union isfuzzy=true to access non-existing view:                                     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true
(View_1 | where x > 0), 
(View_2 | where x > 0),
(View_3 | where x > 0)
| count 
```

|計數|
|---|
|2|

觀察查詢狀態-傳回下列警告：`Failed to resolve entity 'View_3'`

```kusto
// Using union isfuzzy=true and wildcard access:
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true View*, SomeView*, OtherView*
| count 
```

|計數|
|---|
|3|

觀察查詢狀態-傳回下列警告：`Failed to resolve entity 'SomeView*'`

**範例：來源資料行類型不符**
 
```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
union withsource=TableName View_1, View_2
```

|TableName|x_long|x_int|
|---------|------|-----|
|View_1   |1     |     |
|View_2   |      |2    |

```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
let View_3 = view () { print x_long=3 };
union withsource=TableName View_1, View_2, View_3 
```

|TableName|x_long1|x_int |x_long|
|---------|-------|------|------|
|View_1   |1      |      |      |
|View_2   |       |2     |      |
|View_3   |       |      |3     |

從收到後置詞的資料行，與 `x` `View_1` `_long` 名稱 `x_long` 已存在於結果架構中的資料行名稱已重複複製，產生新的資料行-`x_long1`
