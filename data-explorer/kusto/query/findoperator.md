---
title: 尋找操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的 find 操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d1e01f366c1bae677111c67b0e60fde59683706e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245045"
---
# <a name="find-operator"></a>尋找運算子

在一組資料表中尋找符合述詞的資料列。

::: zone pivot="azuredataexplorer"

的範圍 `find` 也可以是跨資料庫或跨叢集。

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"

find in (database('*').*) where Fruit == "apple"

find in (cluster('cluster_name').database('MyDB*'.*)) where Fruit == "apple"
```

::: zone-end

::: zone pivot="azuremonitor"

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"
```

::: zone-end

## <a name="syntax"></a>語法

* `find`[ `withsource` = *ColumnName*] [ `in` `(` *table* [ `,` *table*，...] `)` ] `where`述*詞 [* `project-smart`  |  `project` *ColumnName* [ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*]，...][`,` `pack(*)`]] 

* `find`述*詞 [* `project-smart`  |  `project` *ColumnName*[ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*]，...][`, pack(*)`]] 

## <a name="arguments"></a>引數

::: zone pivot="azuredataexplorer"

* `withsource=`*ColumnName*：選擇性。 根據預設，輸出會包含名為 *source_* 的資料行，其值會指出哪個來源資料表已貢獻每個資料列。 如果有指定，將會使用 *ColumnName* 而不是 *source_*。
在萬用字元比對之後，如果查詢會參考多個資料庫 (的資料表（包括預設資料庫) ），此資料行的值將會有以資料庫限定的資料表名稱。 同樣 *地，如果* 參考了多個叢集，則會在值中顯示叢集和 *資料庫* 的資格。
* *Predicate*述詞： `boolean` 輸入資料表*資料表*[ [expression](./scalar-data-types/bool.md) `,` *資料表*，...] 的資料行上的運算式。它會針對每個輸入資料表中的每個資料列進行評估。 如需詳細資訊，請參閱述詞  [-語法詳細資料](./findoperator.md#predicate-syntax)。
* `Table`:選擇性。 依預設，[ *尋找* ] 會在目前資料庫的所有資料表中尋找：
    *  資料表的名稱，例如 `Events`
    *  查詢運算式，例如 `(Events | where id==42)`
    *  使用萬用字元指定的一組資料表。 例如， `E*` 會形成名稱開頭為之資料庫中所有資料表的聯集 `E` 。
* `project-smart` | `project`：如果未指定， `project-smart` 預設會使用。 如需詳細資訊，請參閱 [輸出架構詳細資料](./findoperator.md#output-schema)。

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*ColumnName*：選擇性。 根據預設，輸出會包含名為 *source_* 的資料行，其值會指出每個資料列所貢獻的來源資料表。 如果有指定，將會使用 *ColumnName* 而不是 *source_*。
* *Predicate*述詞： `boolean` 輸入資料表*資料表*[ [expression](./scalar-data-types/bool.md) `,` *資料表*，...] 的資料行上的運算式。它會針對每個輸入資料表中的每個資料列進行評估。 如需詳細資訊，請參閱述詞  [-語法詳細資料](./findoperator.md#predicate-syntax)。
* `Table`:選擇性。 依預設，[ *尋找* ] 會在所有資料表中搜尋：
    *  資料表的名稱，例如 `Events` 
    *  查詢運算式，例如 `(Events | where id==42)`
    *  使用萬用字元指定的一組資料表。 例如， `E*` 會形成名稱開頭為之所有資料表的聯集 `E` 。
* `project-smart` | `project`：預設會使用 [未指定] `project-smart` 。 如需詳細資訊，請參閱 [輸出架構詳細資料](./findoperator.md#output-schema)。

::: zone-end

## <a name="returns"></a>傳回

轉換*資料表*中的資料列 [ `,` *資料表*，...]，其述*Predicate*詞為 `true` 。 資料列會根據 [輸出架構](#output-schema)來轉換。

## <a name="output-schema"></a>輸出結構描述

**source_ 資料行**

Find 運算子輸出一律會包含具有來源資料表名稱的 *source_* 資料行。 您可以使用參數來重新命名資料行 `withsource` 。

**結果資料行**

不包含述詞評估所使用之任何資料行的來源資料表將會被篩選掉。

使用時 `project-smart` ，將會在輸出中顯示的資料行為：
* 在述詞中明確出現的資料行。
* 所有篩選資料表通用的資料行。

其餘的資料行會封裝成屬性包，並顯示在其他的資料 `pack_` 行中。
由述詞明確參考，並且出現在多個具有多個型別的資料表中的資料行，在結果架構中，每種類型都有不同的資料行。 每個資料行名稱都是從原始資料行名稱和類型（以底線分隔）所構成。

使用 `project` *ColumnName*[ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*]，...][`,` `pack(*)`]:
* 結果資料表會包含清單中所指定的資料行。 如果來源資料表未包含特定資料行，對應資料列中的值將會是 null。
* 使用*ColumnName*指定*ColumnType*時，「結果」中的這個資料行會具有指定的型別，而如果需要，則會將值轉換成該型別。 在評估述詞時，轉換不會影響資料行類型 *。*
* 當 `pack(*)` 使用時，其餘的資料行會封裝成屬性包，並顯示在其他資料 `pack_` 行中。

**pack_ 資料行**

這個資料行會包含屬性包，其中包含所有資料行中不會出現在輸出架構中的資料。 來源資料行名稱將作為屬性名稱，而資料行值將作為屬性值。

## <a name="predicate-syntax"></a>述詞語法

*Find*運算子支援詞彙的替代語法 `* has` ，而且只使用*詞彙*，會在所有輸入資料行之間搜尋詞彙。

如需某些篩選函數的摘要，請參閱 [where 運算子](./whereoperator.md)。

## <a name="notes"></a>注意

* 如果 `project` 子句參考出現在多個資料表中的資料行，而且有多個類型，則型別必須在 project 子句中遵循此資料行參考
* 如果資料行出現在多個資料表中，且有多個類型且 `project-smart` 正在使用中，則結果中的每個類型都會有對應的資料行 `find` ，如[union](./unionoperator.md)中所述。
* 使用 *project-smart*時，述詞、來源資料表集中的變更或資料表架構中的變更，可能會導致輸出架構變更。 如果需要常數結果架構，請改用*project*
* `find` 範圍不能包含 [函數](../management/functions.md)。 若要在尋找範圍中包含函數，請使用[view 關鍵字](./letstatement.md)定義[let 語句](./letstatement.md)。

## <a name="performance-tips"></a>效能秘訣

* 使用 [資料表](../management/tables.md) ，而不是 [表格式運算式](./tabularexpressionstatements.md)。
如果是表格式運算式，find 運算子會切換回 `union` 可能導致效能降低的查詢。
* 如果出現在多個資料表中且有多個類型的資料行是 project 子句的一部分，請在將其傳遞至之前，偏好將 *ColumnType* 新增至 project 子句，以修改資料表 `find` 。
* 將以時間為基礎的篩選準則新增至述詞。 使用 datetime 資料行值或 [ingestion_time ( # B1 ](./ingestiontimefunction.md)。
* 搜尋特定資料行，而不是全文檢索搜尋。
* 最好不要參考出現在多個資料表中的資料行，而且有多個類型。 如果述詞在解析多個類型的這類資料行類型時有效，則查詢會切換回 union。
例如，請參閱 [find 將作為聯集的案例範例](./findoperator.md#examples-of-cases-where-find-will-act-as-union)。
 
## <a name="examples"></a>範例

::: zone pivot="azuredataexplorer"

### <a name="term-lookup-across-all-tables-in-current-database"></a>在目前資料庫中的所有資料表進行詞彙查閱

查詢會從目前資料庫的所有資料表中，尋找任何資料行包含該字的所有資料列 `Kusto` 。
系統會根據 [輸出架構](#output-schema)來轉換產生的記錄。

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>針對目前資料庫中所有符合名稱模式的資料表進行詞彙查閱

此查詢會從目前資料庫中的名稱開頭為的所有資料表，以及任何資料行包含該字的資料表中，尋找所有資料 `K` 列 `Kusto` 。
系統會根據 [輸出架構](#output-schema)來轉換產生的記錄。

```kusto
find in (K*) where * has "Kusto"
```

### <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>在叢集中的所有資料庫中，所有資料表的詞彙查閱

查詢會尋找所有資料庫中所有資料行都包含該字的所有資料表中的所有資料列 `Kusto` 。
此查詢是 [跨資料庫](./cross-cluster-or-database-queries.md) 的查詢。
系統會根據 [輸出架構](#output-schema)來轉換產生的記錄。

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>在叢集中符合名稱模式的所有資料表和資料庫進行詞彙查閱

查詢會尋找所有資料表中的所有資料列，該資料表的名稱開頭為 `K` ，所有資料庫的名稱開頭為，任何資料行都 `B` 包含該字 `Kusto` 。
系統會根據 [輸出架構](#output-schema)來轉換產生的記錄。

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>數個群集中的詞彙查閱

查詢會尋找所有資料表中的所有資料列，該資料表的名稱開頭為 `K` ，所有資料庫的名稱開頭為，任何資料行都 `B` 包含該字 `Kusto` 。
系統會根據 [輸出架構](#output-schema)來轉換產生的記錄。

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>跨所有資料表的詞彙查閱

此查詢會從任何資料行包含單字的所有資料表中，尋找所有資料列 `Kusto` 。
系統會根據 [輸出架構](#output-schema)來轉換產生的記錄。

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>`find`輸出結果的範例  

下列範例會示範如何在 `find` 兩個數據表上使用： *EventsTable1* 和 *EventsTable2*。
假設我們有這兩個數據表的下一個內容：

### <a name="eventstable1"></a>EventsTable1

|Session_Id|層級|EventText|版本
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|資訊|部分 Text1|1.0.0 版
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|錯誤|部分 2-2|1.0.0 版
|28b8e46e-3c31-43cf-83cb-48921c3986fc|錯誤|某些文本段|v. 1.0。1
|8f057b11-3281-45c3-a856-05ebb18a3c59|資訊|部分的，|v 1.1。0

### <a name="eventstable2"></a>EventsTable2

|Session_Id|層級|EventText|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|資訊|其他 Text1|Event1.png
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|資訊|一些其他的等文本|Event2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|錯誤|一些其他的文本段|Event3
|15eaeab5-8576-4b58-8fc6-478f75d8fee4|錯誤|一些其他的部分。|Event4

### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>在通用資料行中搜尋、投射一般和不尋常的資料行，並將其餘專案打包  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|EventText|版本|EventName|pack_
|---|---|---|---|---|
|EventsTable1|部分 2-2|1.0.0 版||{"Session_Id"： "acbd207d-51aa-4df7-bfa7-be70eb68f04e"，"Level"： "Error"}
|EventsTable2|一些其他的文本段||Event3|{"Session_Id"： "acbd207d-51aa-4df7-bfa7-be70eb68f04e"，"Level"： "Error"}


### <a name="search-in-common-and-uncommon-columns"></a>在常見和不尋常的資料行中搜尋

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|EventText|版本|EventName|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|部分 Text1|1.0.0 版
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|部分 2-2|1.0.0 版
|EventsTable2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|其他 Text1||Event1.png

注意：實務上， *EventsTable1* 資料列將會以述詞篩選， ```Version == 'v1.0.0'``` 而 *EventsTable2* 資料列將會以述詞篩選 ```EventName == 'Event1'``` 。

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>使用縮寫標記法在目前資料庫中的所有資料表之間搜尋

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|層級|EventText|pack_|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|資訊|部分 Text1|{"Version"： "1.0.0"}
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|錯誤|部分 2-2|{"Version"： "1.0.0"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|資訊|一些其他的等文本|{"事件名稱"： "Event2"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|錯誤|一些其他的文本段|{"事件名稱"： "Event3"}


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>傳回每個資料列的結果做為屬性包

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|EventsTable1|{"Session_Id"： "acbd207d-51aa-4df7-bfa7-be70eb68f04e"，"Level"： "Information"，"EventText"： "Some Text1"，"Version"： "1.0.0 版"}
|EventsTable1|{"Session_Id"： "acbd207d-51aa-4df7-bfa7-be70eb68f04e"，"Level"： "Error"，"EventText"： "Some &"，"Version"： "1.0.0 版"}
|EventsTable2|{"Session_Id"： "acbd207d-51aa-4df7-bfa7-be70eb68f04e"，"Level"： "Information"，"EventText"： "其他資料 2-"，"事件名稱"： "Event2"}
|EventsTable2|{"Session_Id"： "acbd207d-51aa-4df7-bfa7-be70eb68f04e"，"Level"： "Error"，"EventText"： "其他內容"，"事件名稱"： "Event3"}


## <a name="examples-of-cases-where-find-will-act-as-union"></a>將作為的案例範例 `find``union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>使用非表格式運算式作為尋找運算元

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>參考出現在多個資料表且有多個類型的資料行

假設我們已透過執行下列方式建立兩個數據表： 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* 下列查詢將會執行為 `union` 。

```kusto
find in (Table1, Table2) where ProcessId == 1001
```

輸出結果架構將會是 * (層級：字串、時間戳記、ProcessId_string ProcessId_int) *。

* 下列查詢也會以相同的方式執行 `union` ，但會產生不同的結果架構。

```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

輸出結果架構會 * (層級：字串、時間戳記、ProcessId_string) *
