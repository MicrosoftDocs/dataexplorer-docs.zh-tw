---
title: union 運算子 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 union 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3ce7c2c09e9cd5449accfabc2a7e1cc21e4ed339
ms.sourcegitcommit: d4b359e817e002fba7320132732ce6d9cee97415
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541473"
---
# <a name="union-operator"></a>union 運算子

取得兩個或以上的資料表並傳回這些資料表中的資料列。 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>語法

*T* `| union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

不含輸送輸入的替代表單：

`union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

## <a name="arguments"></a>引數

::: zone pivot="azuredataexplorer"

* `Table`:
    *  資料表名稱，例如 `Events`；或
    *  必須以括弧括住的查詢運算式，例如 `(Events | where id==42)` 或 `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))`；或
    *  使用萬用字元指定的一組資料表。 例如，`E*` 會形成資料庫中所有資料表的聯集，其名稱以 `E` 開頭。
* `kind`: 
    * `inner` - 結果中會有所有輸入資料表共有的資料行子集。
    * `outer` - (預設值)。 結果具有任何輸入中出現的所有資料行。 輸入資料列未定義的資料格會設為 `null`。
* `withsource`=*ColumnName*：若已指定，輸出會包含名為 ColumnName 的資料行，其值會指出哪一個來源資料表貢獻了每個資料列。
如果查詢 (在萬用字元比對之後) 有效地參考多個資料庫 (預設資料庫一律計算在內) 中的資料表，則此資料行的值將具有資料庫限定的資料表名稱。
同樣地，如果參考多個叢集，則值中會出現 __叢集和資料庫__ 資格。 
* `isfuzzy=` `true` | `false`：如果 `isfuzzy` 設為 `true` - 允許對聯集支線進行模糊解析。 `Fuzzy` 適用於 `union` 來源集。 這表示在分析查詢並準備執行時，聯集來源集會縮減為存在並可當下存取的資料表參考集。 如果至少找到一個這類資料表，任何解析失敗都會在查詢狀態結果中產生警告 (每個遺漏參考一個警告)，但不會防止查詢執行；如果沒有成功的解析，查詢將會傳回錯誤。
預設為 `isfuzzy=` `false`。
* *UnionParameters*：格式為 *Name* `=` *Value* 的零個或多個 (以空格分隔) 參數，此種參數可控制資料列比對作業和執行計畫的行為。 支援下列參數： 

  |名稱           |值                                        |描述                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Number*|提示系統：`union` 運算子有多少個並行子查詢應該以平行方式執行。 *預設值*：叢集的單一節點上的 CPU 核心數量 (2 到 16 個)。|
  |`hint.spread`|*Number*|提示系統並行 `union` 子查詢執行應使用多少個節點。 *預設值*：1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  資料表名稱，例如 `Events`
    *  必須以括弧括住的查詢運算式，例如 `(Events | where id==42)`
    *  使用萬用字元指定的一組資料表。 例如，`E*` 會形成資料庫中所有資料表的聯集，其名稱以 `E` 開頭。

> [!NOTE]
> 知道資料表的清單時，請避免使用萬用字元。 某些工作區包含非常大量的資料表，會導致執行效率不佳。 資料表也可能會隨著導致非預期結果的時間而增加。
    
* `kind`: 
    * `inner` - 結果中會有所有輸入資料表共有的資料行子集。
    * `outer` - (預設值)。 結果具有任何輸入中出現的所有資料行。 輸入資料列未定義的資料格會設為 `null`。
* `withsource`=*ColumnName*：若已指定，輸出會包含名為 ColumnName 的資料行，其值會指出哪一個來源資料表貢獻了每個資料列。
如果查詢 (在萬用字元比對之後) 有效地參考多個資料庫 (預設資料庫一律計算在內) 中的資料表，則此資料行的值將具有資料庫限定的資料表名稱。
同樣地，如果參考多個叢集，則值中會出現 __叢集和資料庫__ 資格。 
* `isfuzzy=` `true` | `false`：如果 `isfuzzy` 設為 `true` - 允許對聯集支線進行模糊解析。 `Fuzzy` 適用於 `union` 來源集。 這表示在分析查詢並準備執行時，聯集來源集會縮減為存在並可當下存取的資料表參考集。 如果至少找到一個這類資料表，任何解析失敗都會在查詢狀態結果中產生警告 (每個遺漏參考一個警告)，但不會防止查詢執行；如果沒有成功的解析，查詢將會傳回錯誤。
預設為 `isfuzzy=false`。

::: zone-end

## <a name="returns"></a>傳回

所含資料列數目和所有輸入資料表中的資料列數目一樣多的資料表。

**注意事項**

::: zone pivot="azuredataexplorer"

1. 如果 [let 陳述式](./letstatement.md)使用 [view 關鍵字](./letstatement.md)屬性化，`union` 範圍即可包含這些陳述式
2. `union` 範圍不會包含[函式](../management/functions.md)。 若要在聯集範圍中包含函式，請使用 [view 關鍵字](./letstatement.md)定義 [let 陳述式](./letstatement.md)
3. 如果 `union` 輸入是[資料表](../management/tables.md) (而不是[表格式運算式](./tabularexpressionstatements.md))，而且 `union` 後面接著 [where 運算子](./whereoperator.md)，請考慮使用 [find](./findoperator.md) 取代兩者，以獲得更好的效能。 請注意 `find` 運算子所產生的不同[輸出結構描述](./findoperator.md#output-schema)。 
4. `isfuzzy=true` 僅適用於 `union` 來源解析階段。 一旦決定來源資料表的集合，就不會隱藏其他可能的查詢失敗。
5. 使用 `outer union` 時，結果包含在任何輸入中出現的所有資料行，每個出現的名稱和類型都有一個資料行。 這表示如果資料行出現在多個資料表中而且有多個類型，則在 `union` 的結果中，每個類型都會有對應的資料行。 此資料行名稱的尾端會加上 '_'，後面接著來源資料行[類型](./scalar-data-types/index.md)。

::: zone-end

::: zone pivot="azuremonitor"

1. 如果 [let 陳述式](./letstatement.md)使用 [view 關鍵字](./letstatement.md)屬性化，`union` 範圍即可包含這些陳述式
2. `union` 範圍不會包含函式。 若要在聯集範圍中包含函式，請使用 [view 關鍵字](./letstatement.md)定義 [let 陳述式](./letstatement.md)
3. 如果 `union` 輸入是資料表 (而不是[表格式運算式](./tabularexpressionstatements.md))，而且 `union` 後面接著 [where 運算子](./whereoperator.md)，請考慮使用 [find](./findoperator.md) 取代兩個，以獲得更好的效能。 請注意 `find` 運算子所產生的不同[輸出結構描述](./findoperator.md#output-schema)。 
4. `isfuzzy=` `true` 僅適用於 `union` 來源解析的階段。 一旦決定來源資料表的集合，就不會隱藏其他可能的查詢失敗。
5. 使用 `outer union` 時，結果包含在任何輸入中出現的所有資料行，每個出現的名稱和類型都有一個資料行。 這表示如果資料行出現在多個資料表中而且有多個類型，則在 `union` 的結果中，每個類型都會有對應的資料行。 此資料行名稱的尾端會加上 '_'，後面接著來源資料行[類型](./scalar-data-types/index.md)。

::: zone-end


## <a name="example-tables-with-string-in-name-or-column"></a>範例：名稱或資料行中有字串的資料表

```kusto
union K* | where * has "Kusto"
```

在名稱以 `K` 開頭的資料庫中所有資料表的資料列，而且其中的任何資料行包含 `Kusto` 這個字。

## <a name="example-distinct-count"></a>範例：相異計數

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

**範例：使用 `isfuzzy=true`**
 
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

觀察查詢狀態 - 傳回下列警告：`Failed to resolve entity 'View_3'`

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

觀察查詢狀態 - 傳回下列警告：`Failed to resolve entity 'SomeView*'`

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

`View_1` 中的資料行 `x` 收到後置詞 `_long`，而且結果結構描述中已經存在名為 `x_long` 的資料行，因此會刪除重複的資料行名稱，進而產生新的資料行 - `x_long1`
