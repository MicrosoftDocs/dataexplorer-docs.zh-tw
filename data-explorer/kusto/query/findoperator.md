---
title: 尋找運算子 ─ Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的查找運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 495eb15c13a5691df8b0a2f3c2996c5aac58eb0a
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663803"
---
# <a name="find-operator"></a>尋找運算子

查找與一組表的謂詞匹配的行。

::: zone pivot="azuredataexplorer"

的範圍`find`也可以是跨資料庫或跨群集。

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

* `find`【`withsource`=*欄位*名稱 】`in``(`[*Table*`,`表 *] 表*, ...]`)`[ `where`*謂詞*`project-smart` | `:`*ColumnType*`,`*ColumnName**ColumnType**ColumnName*]`:`欄位 : 欄型態 " 欄位 : `project`[`,` `pack(*)`]] 

* `find`*謂*`project-smart` | 詞`project``:`[*欄位*:`,`*欄型態*】 [*欄位*:`:`*欄型態** ...[`, pack(*)`]] 

## <a name="arguments"></a>引數

::: zone pivot="azuredataexplorer"

* `withsource=`*列名*:可選。 默認情況下,輸出將包括一個稱為*source_* 的列,其值指示每行貢獻的原始表。 如果指定,將使用*列名*而不是*source_* 。
如果查詢有效(通配符匹配後)引用來自多個資料庫的表(默認資料庫始終計數),則此列的值將具有資料庫限定的表名稱。 同樣,如果引用了多個群集,則值中將存在__群集和資料庫__限定。
* *謂詞*:[請參考詳細資訊](./findoperator.md#predicate-syntax)。 `boolean`在輸入*表格*的[欄上表達](./scalar-data-types/bool.md)[`,` *表*, ...]它為每個輸入表中的每一行計算。 
* `Table`：選擇性。 預設情況下 *,尋找*目前資料庫中的所有表
    *  表的名稱,如`Events`
    *  查詢運算式，例如 `(Events | where id==42)`
    *  使用萬用字元指定的一組資料表。 例如,`E*`將形成資料庫中名稱`E`開始 的所有表的合併。
* `project-smart` | `project`:[詳細資訊](./findoperator.md#output-schema)(如果`project-smart`未指定 )將預設使用

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*列名*:可選。 默認情況下,輸出將包括一個稱為*source_* 的列,其值指示每行貢獻的原始表。 如果指定,將使用*列名*而不是*source_* 。
* *謂詞*:[請參考詳細資訊](./findoperator.md#predicate-syntax)。 `boolean`在輸入*表格*的[欄上表達](./scalar-data-types/bool.md)[`,` *表*, ...]它為每個輸入表中的每一行計算。 
* `Table`：選擇性。 預設情況下 *,尋找*所有表格搜尋
    *  表的名稱,如`Events`
    *  查詢運算式，例如 `(Events | where id==42)`
    *  使用萬用字元指定的一組資料表。 例如,`E*`將形成名稱`E`開始 的所有表的合併。
* `project-smart` | `project`:[詳細資訊](./findoperator.md#output-schema)(如果`project-smart`未指定 )將預設使用

::: zone-end

## <a name="returns"></a>傳回值

"*謂詞*`true`'*中的行*的`,`*轉換。* 行根據輸出架構進行轉換,如下所述。

## <a name="output-schema"></a>輸出架構

**source_欄**

find 運算符輸出將始終包含具有源表名稱*的source_* 列。 可以使用`withsource`參數重新命名列。

**結果欄**

將篩選出不包含謂詞計算使用的任何列的源表。

使用`project-smart`時,輸出中顯示的欄位將是:
1. 在謂詞中顯示式顯示的欄
2. 所有篩選表共有的列 其餘列將打包到屬性包中,並將顯示在`pack_`其他 列中。
謂詞顯式引用並出現在具有多種類型的多個表中的列,在每個此類類型的結果架構中將有一個不同的列。 每個列名稱都將從原始列名稱和類型構造,由下劃線分隔。

使用`project`*欄位*名稱 ,`:``:`*欄位型態*`,`( 欄位 ) [*欄位*型*態** ...[`,` `pack(*)`]:
1. 結果表將包括清單中指定的欄。 如果源表不包含特定列,則相應行中的值將為空。
2. 指定*欄型態*以及*列名*時,**結果**中的此欄將具有給定的類型,如果需要,這些值將轉換為該類型。 請注意,在計算*謂詞*時,這對列類型沒有影響。
3. 使用`pack(*)`時,其餘列將打包到屬性套件中,並顯示在`pack_`其他 欄中

**pack_欄**

此列將包含一個屬性包,其中包含輸出架構中未顯示的所有列中的數據。 源列名稱將用作屬性名稱,列值將用作屬性值。

## <a name="predicate-syntax"></a>謂詞語法

有關某些篩選功能摘要,請參閱[位置運算子](./whereoperator.md)。

find 運算子支援`* has`*字詞*的替代語法,使用僅*字*詞將跨所有輸入列搜尋術語

## <a name="notes"></a>注意

* 如果`project`子句引用出現在多個表中且具有多種類型的欄,則類型必須遵循專案子句中的此列引用
* 如果列出現在多個表中,並且有多個類型並且`project-smart`正在使用中,則結果中`find`將 針對每一類型對應一列,如[聯合](./unionoperator.md)中所述
* 使用*專案智慧*時,謂詞、源表集或表架構中的更改可能會導致對輸出架構進行更改。 如果需要恆定結果架構,請使用*專案*
* `find`範圍不能包含[函數](../management/functions.md)。 尋找作用網域包含函數 -[使用 檢視關鍵字](./letstatement.md)定義[let 語句](./letstatement.md)

## <a name="performance-tips"></a>效能提示

* 使用[tables](../management/tables.md)表[格表示式](./tabularexpressionstatements.md)- 在表格表示式的情況下,find 運算子會回落到可能導致效能下降`union`的 查詢
* 如果出現在多個表中且具有多個類型的欄是專案子句的一部分,則在將表傳遞給`find`表之前,更喜歡向專案子句添加*ColumnType*而不是修改表(請參閱前面的提示)
* 將基於時間的篩選器加入到謂詞(使用日期時間列值或[ingestion_time()](./ingestiontimefunction.md)
* 更喜歡在特定欄中搜尋而不是全文搜尋 
* 不希望引用出現在多個表中且具有多種類型的顯式列。 如果謂詞在為多個類型解析此類列類型時有效,則查詢將回退到聯合

[檢視例](./findoperator.md#examples-of-cases-where-find-will-perform-as-union)

## <a name="examples"></a>範例

::: zone pivot="azuredataexplorer"

###  <a name="term-lookup-across-all-tables-in-current-database"></a>跨所有表的術語尋找(在目前資料庫中)

下一個查詢查找目前資料庫中所有表中的所有行,其中任何欄位都包含單`Kusto`詞 。 生成的記錄根據[輸出架構](#output-schema)進行轉換。 

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>跨與名稱模式符合的所有表的術語尋找(在目前資料庫中)

下一個查詢查找目前資料庫中所有表的所有行,其名稱以`K`開頭,並且任何列都包含單詞`Kusto`。
生成的記錄根據[輸出架構](#output-schema)進行轉換。 

```kusto
find in (K*) where * has "Kusto"
```

###  <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>跨所有資料庫的所有表(在群集中)尋找術文尋找

下一個查詢查找所有資料庫中所有表中的所有行,其中任何欄位都包含單`Kusto`詞 。 這是[跨資料庫查詢](./cross-cluster-or-database-queries.md)。 生成的記錄根據[輸出架構](#output-schema)進行轉換。 

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>跨與名稱模式符合的所有表和資料庫的術語尋找(在群集中)

下一個查詢查找所有表中的名稱以`K`名稱開`B`頭 且任何列包含`Kusto`單詞 的所有資料庫中的所有行。 生成的記錄根據[輸出架構](#output-schema)進行轉換。 

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>多個群組的術語尋找

下一個查詢查找所有表中的名稱以`K`名稱開`B`頭 且任何列包含`Kusto`單詞 的所有資料庫中的所有行。 生成的記錄根據[輸出架構](#output-schema)進行轉換。 

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>跨所有表進行術文尋找

下一個查詢查找所有表中包含單詞`Kusto`的所有行。 生成的記錄根據[輸出架構](#output-schema)進行轉換。 

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>輸出結果`find`範例  

以下範例展示如何`find`在兩個表上使用:事件表1 和事件表2。 假設我們有以下兩個表的下一個內容: 

### <a name="eventstable1"></a>事件表1

|Session_Id|層級|事件文字|版本
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|資訊|一些文字1|v1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|錯誤|一些文字2|v1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|錯誤|一些文字3|v1.0.1
|8f057b11-3281-45c3-a856-05ebb18a3c59|資訊|一些文字4|v1.1.0

### <a name="eventstable2"></a>事件表2

|Session_Id|層級|事件文字|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|資訊|其他一些文字1|事件1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|資訊|其他一些文字2|事件2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|錯誤|其他一些文字3|事件3
|15eaaab5-8576-4b58-8fc6-478f75d8fee4|錯誤|其他一些文字4|活動4


### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>在常見列中搜索,投影常見和不常見的列,並打包其餘  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|事件文字|版本|EventName|pack_
|---|---|---|---|---|
|事件表1|一些文字2|v1.0.0||{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","級別":"錯誤"]
|事件表2|其他一些文字3||事件3|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","級別":"錯誤"]


### <a name="search-in-common-and-uncommon-columns"></a>以常見和不常見的列搜尋

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|事件文字|版本|EventName|
|---|---|---|---|---|
|事件表1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|一些文字1|v1.0.0
|事件表1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|一些文字2|v1.0.0
|事件表2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|其他一些文字1||事件1

注意:實際上,*事件表1*行```Version == 'v1.0.0'```將用 謂詞進行篩選,*事件表2*行將用```EventName == 'Event1'```謂詞進行篩選。

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>使用縮寫表示法在目前資料庫中的所有表中搜尋

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|層級|事件文字|pack_|
|---|---|---|---|---|
|事件表1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|資訊|一些文字1|{"版本":"v1.0.0"]
|事件表1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|錯誤|一些文字2|{"版本":"v1.0.0"]
|事件表2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|資訊|其他一些文字2|{"事件名稱":"事件 2"}
|事件表2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|錯誤|其他一些文字3|{"事件名稱":"事件3"|


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>將每行的結果作為屬性套件傳回

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|事件表1|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","級別":"資訊","事件文本":"某些文本1","版本":"v1.0.0"]
|事件表1|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","級別":"錯誤","事件文本":"某些文本2","版本":"v1.0.0"]
|事件表2|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","級別":"資訊","事件文本":"其他文本2","事件名稱":"事件2"
|事件表2|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","級別":"錯誤","事件文本":"其他文字3","事件名稱":"事件3"


## <a name="examples-of-cases-where-find-will-perform-as-union"></a>案例示例,其中`find`將執行`union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>使用非表格表示式作為尋找操作數

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>參考多個表中且具有多種類型的欄

假設我們通過運行建立了兩個表: 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* 以下查詢將做為`union`執行:
```kusto
find in (Table1, Table2) where ProcessId == 1001
```

輸出結果架構為 __(級別:字串、時間戳、ProcessId_string、ProcessId_int)__

* 以下查詢也將執行,但`union`將生成不同的結果架構:
```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

輸出結果架構為 __(等級:字串、時間戳、ProcessId_string)__