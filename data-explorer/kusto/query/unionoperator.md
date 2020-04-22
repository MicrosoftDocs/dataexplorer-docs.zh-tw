---
title: 聯合運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的聯合運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b62c259e1abf1ff0e0e98a90ac4da5a000db5320
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765415"
---
# <a name="union-operator"></a>union 運算子

取得兩個或以上的資料表並傳回這些資料表中的資料列。 

```kusto
Table1 | union Table2, Table3
```

**語法**

*T* `| union` =*以參數*`kind=``inner`|`outer``withsource=``isfuzzy=``true`|`false`[ * 欄*位名稱*`,` *=*= 表 =*表** ...  

沒有導管輸入的替代表單:

`union`【*Paro 參數*】`kind=` `inner` 【 `isfuzzy=` `outer` * * * * * 表 * 表 * ... | `true` | `false` `,` *Table* *ColumnName* *Table* `withsource=`  

**引數**

::: zone pivot="azuredataexplorer"

* `Table`:
    *  資料表名稱，例如 `Events`；或
    *  必須與括弧(如`(Events | where id==42)``(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))`或 ;或
    *  使用萬用字元指定的一組資料表。 例如,`E*`將形成資料庫中名稱`E`開始 的所有表的合併。
* `kind`: 
    * `inner` - 結果中會有所有輸入資料表共有的資料行子集。
    * `outer`- (預設)。 結果具有出現在任何輸入中的所有列。 沒有輸入行定義的儲存格設定為`null`。
* `withsource`=*欄位*:如果指定,輸出將包括一個名為*列Name*的欄,其值指示每行貢獻的源表。
如果查詢有效(通配符匹配後)引用來自多個資料庫的表(默認資料庫始終計數),則此列的值將具有資料庫限定的表名稱。
同樣,如果引用了多個群集,則值中將存在__群集和資料庫__限定。 
* `isfuzzy=``true` `isfuzzy`  |  :如果 設定為 - 允許對聯合腿進行模糊解析。 `false` `true` `Fuzzy`套用來源集`union`。 這意味著,在分析查詢並準備執行時,聯合源集將減少到當時存在且可訪問的表引用集。 如果至少找到一個此類表,則任何解析失敗都會在查詢狀態結果中產生警告(每個缺少的引用一個),但不會阻止查詢執行;如果沒有解決方案成功 - 查詢將返回錯誤。
預設值為 `isfuzzy=` `false`。
* *聯合參數*:以*名稱*`=`*值*的形式控制行匹配操作和執行計劃的行為的零個或多個(空間分隔)參數。 支援下列參數： 

  |名稱           |值                                        |描述                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*數量*|提示系統應並行執行`union`運算符的併發子查詢數。 *默認值*:群集單個節點上的 CPU 內核量(2 到 16)。|
  |`hint.spread`|*數量*|提示系統併發`union`子查詢執行應使用多少個節點。 *默認值*:1。|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  表的名稱,例如`Events`
    *  必須與括弧一起包含的查詢表示式,例如`(Events | where id==42)`
    *  使用萬用字元指定的一組資料表。 例如,`E*`將形成資料庫中名稱`E`開始 的所有表的合併。
* `kind`: 
    * `inner` - 結果中會有所有輸入資料表共有的資料行子集。
    * `outer`- (預設)。 結果具有出現在任何輸入中的所有列。 沒有輸入行定義的儲存格設定為`null`。
* `withsource`=*欄位*:如果指定,輸出將包括一個名為*列Name*的欄,其值指示每行貢獻的源表。
如果查詢有效(通配符匹配後)引用來自多個資料庫的表(默認資料庫始終計數),則此列的值將具有資料庫限定的表名稱。
同樣,如果引用了多個群集,則該值中將存在__群集和資料庫__限定。 
* `isfuzzy=``true` `isfuzzy`  |  :如果 設定為 - 允許對聯合腿進行模糊解析。 `false` `true` `Fuzzy`套用來源集`union`。 這意味著,在分析查詢並準備執行時,聯合源集將減少到當時存在且可訪問的表引用集。 如果至少找到一個此類表,則任何解析失敗都會在查詢狀態結果中產生警告(每個缺少的引用一個),但不會阻止查詢執行;如果沒有解決方案成功 - 查詢將返回錯誤。
預設值為 `isfuzzy=false`。

::: zone-end

**傳回**

所含資料列數目和所有輸入資料表中的資料列數目一樣多的資料表。

**注意事項**

::: zone pivot="azuredataexplorer"

1. `union`範圍可以包括[let 語句](./letstatement.md),如果這些語句與[檢視關鍵字](./letstatement.md)屬性化
2. `union`範圍將不含[函數](../management/functions.md)。 要在同盟欄位中包含函數,請使用[檢視關鍵字](./letstatement.md)定義[let 語句](./letstatement.md)
3. 如果`union`輸入是[表](../management/tables.md)格[(與表格表示式](./tabularexpressionstatements.md)`union`相反),則後跟 where[運算子](./whereoperator.md),為了更好的性能,請考慮將兩者取代為[find](./findoperator.md)。 請注意`find`運算子產生的不同[輸出架構](./findoperator.md#output-schema)。 
4. `isfuzzy=true`僅適用於`union`源解析階段。 確定源表集后,將不會抑制可能的其他查詢失敗。
5. 使用`outer union`時,結果具有任何輸入中出現的所有列,每個名稱和類型匹配項都有一列。 這意味著,如果列出現在多個表中並且具有多種類型,則將在`union`結果中為每一種類型具有相應的列。 這裡的名稱後置為"*",後跟原體列[型態](./scalar-data-types/index.md)。

::: zone-end

::: zone pivot="azuremonitor"

1. `union`範圍可以包括[let 語句](./letstatement.md),如果這些語句與[檢視關鍵字](./letstatement.md)屬性化
2. `union`作用域將不包括函數。 在同盟範圍中包含函數 -[使用 檢視關鍵字](./letstatement.md)定義[let 語句](./letstatement.md)
3. 如果`union`輸入是表(與[表格表達式](./tabularexpressionstatements.md)相反),後`union`跟where[運算符](./whereoperator.md),請考慮用[Find](./findoperator.md)替換兩者以獲得更好的性能。 請注意`find`管理員產生的不同[輸出架構](./findoperator.md#output-schema)。 
4. `isfuzzy=``true`僅適用於源解析的`union`階段。 確定源表集后,將不會抑制可能的其他查詢失敗。
5. 使用`outer union`時,結果具有任何輸入中出現的所有列,每個名稱和類型匹配項都有一列。 這意味著,如果列出現在多個表中並且具有多種類型,則將在`union`結果中為每一種類型具有相應的列。 這裡的名稱後置為"*",後跟原體列[型態](./scalar-data-types/index.md)。

::: zone-end


**範例**

```kusto
union K* | where * has "Kusto"
```

資料庫中名稱以`K`開頭的所有表的行,其中任何列都包含單`Kusto`詞 。

**範例**

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

**範例:使用`isfuzzy=true`**
 
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

|Count|
|---|
|2|

觀察查詢狀態 - 傳回以下警告:`Failed to resolve entity 'View_3'`

```kusto
// Using union isfuzzy=true and wildcard access:
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true View*, SomeView*, OtherView*
| count 
```

|Count|
|---|
|3|

觀察查詢狀態 - 傳回以下警告:`Failed to resolve entity 'SomeView*'`

**範例:來源類型不符**
 
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

從`x``View_1`接收後綴的`_long`列 ,`x_long`當名為 的列在結果架構中已存在時,列名稱被去重複,生成一個新的列-`x_long1`
