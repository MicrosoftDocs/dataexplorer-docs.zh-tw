---
title: 擴展區(資料分片)管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的擴展區(數據分片)管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e94b830809bf079e612b477292d00d2dbe85e60e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744735"
---
# <a name="extents-data-shards-management"></a>範圍(資料分片)管理

數據分片在 Kusto 中稱為**延伸盤區**,所有命令都使用「範圍」或「範圍」作為同義詞。

## <a name="show-extents"></a>.顯示範圍

**叢集等級**

`.show` `cluster` `extents` [`hot`]

顯示群集中存在的範圍(資料碎片)的資訊。
如果`hot`指定 - 僅顯示預期位於熱緩存中的擴展盤區。

**資料庫等級**

`.show``database`*資料庫*`extents`名稱`(`=*範圍代碼1*`,`...`,`*範圍IdN*`)``hot`[`where` `tags` `has` |`!has` ]`and` | `contains` | ( `!has` `!contains` )|標籤1 [|) 標籤2 .* `tags` `has` `!contains` `contains` | *Tag1* * *

顯示指定資料庫中存在的區位區(資料分片)的資訊。
如果`hot`指定 - 僅顯示預期位於熱緩存中的擴展盤區。

**資料表層級**

`.show``table`*表*`extents`名`(`=*範圍代碼1*`,`...`,`*範圍IdN*`)``hot`[`where` `tags` `has` |`!has` ]`and` | `contains` | ( `!has` `!contains` )|標籤1 [|) 標籤2 .* `tags` `has` `!contains` `contains` | *Tag1* * *

`.show`表格*名稱 1...* `(` `tables` `,``,`*表格名稱N* `)` `extents` `(`=*範圍代碼1*`,`...`,`*範圍IdN*`)``hot`[`where` `tags` `has` |`!has` ]`and` | `contains` | ( `!has` `!contains` )|標籤1 [|) 標籤2 .* `tags` `has` `!contains` `contains` | *Tag1* * *

顯示指定表中存在的區位(資料分片)資訊(資料庫取自命令的上下文)。
如果`hot`指定 - 僅顯示預期位於熱緩存中的擴展盤區。

**注意：**

* 使用資料庫和/或表級命令比篩選(添加`| where DatabaseName == '...' and TableName == '...'`) 群集級命令的結果要快得多。
* 如果提供了萬有範圍的I,則返回的數據集僅限於這些範圍。
    * 同樣,這比篩選(添加到`| where ExtentId in(...)`)"裸"命令的結果要快得多。
* 指定`tags`過濾器時:
  * 返回的清單僅限於標記集合服從*所有*提供的標記篩選器的擴展盤區。
    * 同樣,這比篩選(添加`| where Tags has '...' and Tags contains '...'`)"裸"命令的結果要快得多。
  * `has`篩選器是相等篩選器:未使用任一指定標記標記的擴展盤區將被篩選出來。
  * `!has`篩選器是相等負篩選器:使用任一指定標記標記的擴展盤區將被過濾掉。
  * `contains`篩選器是不區分大小寫的子字串篩選器:沒有指定字串作為其任何標記的子字串的擴展盤區將被過濾掉。 
  * `!contains`篩選器是不區分大小寫的子字串負篩選器:將篩選出具有指定字串作為其任何標記子字串的擴展盤區。 
  
  * **範例**
    * 表中`E``T`的擴展區使用標`aaa``BBB`記`ccc`和 標記標記。
    * 此查詢將傳`E`回 : 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * 此查詢*不會*返回`E`(因為它未使用等於的標記標`aa`記 標記。
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * 此查詢將傳`E`回 : 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|輸出參數 |類型 |描述 
|---|---|---
|放大縮小字型功能 放大縮小字型功能 |Guid |範圍的識別碼 
|DatabaseName |String |該範圍所屬的資料庫 
|TableName |String |範圍所屬的表 
|馬克斯·斯朗森 |Datetime |建立範圍時的日期與時間(對合併範圍 - 原始範圍之間建立時間的最大) 
|原始大小 |Double |範圍資料的原始大小(以位元組為單位) 
|範圍大小 |Double |記憶體中範圍的大小(壓縮 + 索引) 
|壓縮大小 |Double |記憶體中範圍資料的壓縮大小 
|IndexSize |Double |範圍資料的索引大小 
|區塊 |long |範圍中的資料塊量 
|使用者分佈 |long |範圍中的資料段數量 
|配置的資料節點 |String | 已棄用(空字串)
|增益資料節點 |String |已棄用(空字串)
|範圍容器 Id |String | 範圍中的範圍容器的識別碼
|RowCount |long |範圍中的行數
|最小建立on |Datetime |建立範圍時的日期與時間(對合併範圍 - 原始範圍之間建立時間的最小) 
|Tags|String|為範圍定義的標記(如果有) 
 
**範例**

```kusto 
// Show volume of extents being created per hour in a specific database
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  

// Show volume of data arriving by table per hour 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart

// Show data size distribution by table 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName

// Show all extents in the database named 'GamesDB' 
.show database GamesDB extents

// Show all extents in the table named 'Games' 
.show table Games extents

// Show all extents in the tables named 'TaggingGames1' and 'TaggingGames2', tagged with both 'tag1' and 'tag2' 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
``` 
 
## <a name="merge-extents"></a>.合併範圍

**語法**

`.merge``[async | dryrun]`*表名*`(` *GUID1* =`,` *GUID2* ...]`)``[with(rebuild=true)]`

此命令合併由其 ID 在指定表中指示的範圍(請參閱:[範圍(資料碎片)概述](extents-overview.md))。

合併操作有兩種類型:*合併*(重建索引 *)和重建(* 完全重新生成資料)。

* `async`: 操作將以非同步方式執行 - 結果將是操作 ID (GUID),可以使用該 ID`.show operations <operation ID>`執行以查看命令的狀態&狀態。
* `dryrun`:該操作將僅列出應合併的擴展盤區,但不會實際合併它們。 
* `with(rebuild=true)`:將重建範圍(將重新生成資料),而不是合併(將重建索引)。

**傳回輸出**

輸出參數 |類型 |描述 
---|---|---
原始範圍Id |字串 |源表中原始範圍的唯一標識符 (GUID),該識別碼已合併到目標範圍。 
結果範圍Id |字串 |從源範圍創建的結果範圍的唯一標識符 (GUID)。 失敗後 - "失敗"或"放棄"。
Duration |時間範圍 |完成合併操作所花的時間段。

**範例**

在`MyTable`中 重建兩個特定擴展盤區,非同步執行
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

合併`MyTable`的兩個特定擴充盤區,同步執行
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**注意事項**
- 通常,`.merge`命令很少應手動運行,並且它們在 Kusto 叢集的後台自動執行(根據為其中的表和資料庫定義的[合併策略](mergepolicy.md))。  
  - 合併[策略](mergepolicy.md)下記錄了有關將多個擴展盤區合併為單個範圍的標準的一般注意事項。
- `.merge`操作具有可能的最終狀態`Abandoned`(在使用操作 ID`.show operations`執行時 可以看到 ) - 此狀態表示源擴展盤區未合併在一起,因為源表中不再存在某些源擴展盤區。
  - 這種狀態預計將在發生,例如:
     - 源範圍已作為表保留的一部分被刪除。
     - 源範圍已移動到其他表。
     - 源表已完全刪除或重新命名。

## <a name="move-extents"></a>.移動範圍

**語法**

`.move`[`async``extents``all``from`]`table`*原始表名稱*`to``table`*目標表名稱*

`.move``async` `extents` [ `(` ] *GUID1* [`,` *GUID2* ...`)``to``table`來源表名稱 目標表名稱`from``table`*DestinationTableName**SourceTableName* 

`.move``async` `extents` [ `to`  <| *query* ]*目標表名稱*查詢`table`

此命令在特定資料庫的上下文中運行,並將指定的擴展盤區從源表以事務方式移動到目標表。
需要來源與目標表的[表管理員權限](../management/access-control/role-based-authorization.md)。

* `async`(可選)指定命令是否非同步執行(在這種情況下,將返回操作 ID (Guid),並且可以使用[.show 操作](operations.md#show-operations)命令監視操作的狀態)。
    * 如果使用此選項,可以檢索成功執行的結果[.show 操作詳細資訊](operations.md#show-operation-details)命令)。

有三種方法可以指定要移動的擴展盤區:
1. 要移動特定表的所有範圍。
2. 通過在源表中顯式指定範圍指示。
3. 通過提供其結果指定源表中的擴展區指示的查詢。

**限制**
- 源表和目標表都必須位於上下文資料庫中。 
- 源表中的所有列都應存在於具有相同名稱和數據類型的目標表中。

**使用查詢指定範圍**

```kusto 
.move extents to table TableName <| ...query... 
```

擴展區使用 Kusto 查詢指定,該查詢返回具有名為「天度Id」的列的記錄集。 

**傳回輸出**(用於同步執行)

輸出參數 |類型 |描述 
---|---|---
原始範圍Id |字串 |源表中原始範圍的唯一標識符 (GUID),該識別碼已移動到目標表。 
結果範圍Id |字串 |結果範圍的唯一標識碼 (GUID),該識別碼已由源表移動到目標表。 失敗後 - "失敗"
詳細資料 |字串 |包括故障詳細資訊,以防操作失敗。

**範例**

將表格`MyTable`的所有延伸區移到表`MyOtherTable`格 。
```kusto
.move extents all from table MyTable to table MyOtherTable
```

將 2 個特定範圍(按其範圍指示)`MyTable`從表`MyOtherTable`移動到表格 。
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

將所有擴充區從 2`MyTable1``MyTable2`個特定`MyOtherTable`表格 (、 ) 移至表格 。
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**範例輸出** 

|原始範圍Id |結果範圍Id| 詳細資料
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

## <a name="drop-extents"></a>.下降範圍

從指定的資料庫/表刪除範圍。 此命令具有多個變體:在一個變體中,要刪除的擴展區由 Kusto 查詢指定,而在其他變體中,擴展區使用下面描述的微型語言指定。 
 
### <a name="specifying-extents-with-a-query"></a>使用查詢指定範圍

需要提供查詢傳回的擴充盤區的每個表格表[管理員權限](../management/access-control/role-based-authorization.md)。

丟棄範圍(或只是報告它們,如果使用,`whatif`則不實際丟棄):

**語法**

`.drop``extents` [`whatif`] <**查詢*

擴展區使用 Kusto 查詢指定,該查詢返回具有名為「天度Id」的列的記錄集。 
 
### <a name="dropping-a-specific-extent"></a>刪除特定範圍

在指定表格名稱的情況下,需要[表管理員權限](../management/access-control/role-based-authorization.md)。

在未指定表格名稱的情況下,需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)。

**語法**

`.drop``extent`*延伸區*`from`Id =*表名*|

### <a name="dropping-specific-multiple-extents"></a>刪除特定的多個延伸盤區

在指定表格名稱的情況下,需要[表管理員權限](../management/access-control/role-based-authorization.md)。

在未指定表格名稱的情況下,需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)。

**語法**

`.drop`範圍代碼1 *ExtentId1* ... `(` `extents` `,`*範圍 IdN* `)` =`from` *表名*|

### <a name="specifying-extents-by-properties"></a>依屬性指定範圍

在指定表格名稱的情況下,需要[表管理員權限](../management/access-control/role-based-authorization.md)。

在未指定表格名稱的情況下,需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)。

`.drop``extents` [`older` `limit` *N* *TableName* | `all``tables``trim` *LimitCount*N `from` `extentsize` | `datasize``MB` `by` ( )* | ( 表名`bytes`) [ ( ) N ( ) * [ 限制計數 ]`GB` |  *N* `days` | `hours`

* `older`:將僅刪除早於*N*天/小時的範圍。 
* `trim`: 這個操作會修剪資料庫中的資料,直到範圍與所需大小(MaxSize) 符合 
* `limit`:該操作將應用於第一個*限制計數*範圍 

該命令支援模擬 (`.drop-pretend``.drop`而不是 ) 模式,該模式生成輸出,就像命令會運行一樣,但實際沒有執行它。

**範例**

從資料庫中的所有表中移除 10 天以前建立的所有擴充盤區`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

刪除表`Table1``Table2`與中的所有延伸盤區,其建立時間超過 10 天前:

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

模擬模式:顯示命令會刪除哪些範圍(範圍 ID 參數不適用於此命令):

```kusto
.drop-pretend extents older 10 days from all tables
```

從「測試表」中移除所有延伸盤區:

```kusto
.drop extents from TestTable
```
 
**傳回輸出**

|輸出參數 |類型 |描述 
|---|---|---
|放大縮小字型功能 放大縮小字型功能 |String |由於命令而移除的延伸區 Id 
|TableName |String |表名稱,其中範圍屬於  
|已建立 |Datetime |時間戳,包含有關最初建立延伸區的時間戳 
 
**範例輸出** 

|範圍識別碼 |資料表名稱 |建立於 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178 

## <a name="replace-extents"></a>.取代延伸盤區

**語法**

`.replace``async` `extents` [ `in` `},{`*DestinationTableName*`<| 
{`*query for extents to be dropped from table*] 目標表名稱 查詢,用於從表*查詢中移除要移動到表的延伸區*`table``}`

此命令在特定資料庫的上下文中運行,將指定的擴展盤區從其源表移動到目標錶,並從目標表中刪除指定的擴展盤區。
所有放置和移動操作都在單個事務中完成。

需要來源與目標表的[表管理員權限](../management/access-control/role-based-authorization.md)。

* `async`(可選)指定命令是否非同步執行(在這種情況下,將返回操作 ID (Guid),並且可以使用[.show 操作](operations.md#show-operations)命令監視操作的狀態)。
    * 如果使用此選項,可以檢索成功執行的結果[.show 操作詳細資訊](operations.md#show-operation-details)命令)。

透過提供 2 個查詢,指定應刪除或移動哪些延伸盤區
- *查詢要從表格中移除的延伸碟區*- 此查詢的結果指定範圍指示  
應從目標表中刪除。
- *要移動到表的擴展盤區查詢*- 此查詢的結果指定源表中應移動到目標表的擴展區指示。

兩個查詢都應返回一個記錄集,其中列名為"天地 Id"。

**限制**
- 源表和目標表都必須位於上下文資料庫中。 
- 查詢指定的所有擴展區 *(即要從表中刪除的擴展盤區*)都應屬於目標表。
- 源表中的所有列都應存在於具有相同名稱和數據類型的目標表中。

**傳回輸出**(用於同步執行)

輸出參數 |類型 |描述 
---|---|---
原始範圍Id |字串 |源表中原始範圍的唯一識別碼 (GUID),該識別子已移動到目標表,或 - 目標表中已刪除的擴展盤區。
結果範圍Id |字串 |結果範圍的唯一標識碼 (GUID),該識別碼從源表移動到目標表,或 - 空,以防範圍從目標表中刪除。 失敗後 - "失敗"
詳細資料 |字串 |包括故障詳細資訊,以防操作失敗。

**範例**

將所有延伸碟區從 2`MyTable1``MyTable2`個特定表格 (、`MyOtherTable` `MyOtherTable` `drop-by:MyTag`) 移至表 , 並丟棄標記的所有延伸盤區:

```kusto
.replace extents in table MyOtherTable <|
    {.show table MyOtherTable extents where tags has 'drop-by:MyTag'},
    {.show tables (MyTable1,MyTable2) extents}
```

**範例輸出** 

|原始範圍Id |結果範圍Id |詳細資料
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

*注意*： 
> [!NOTE]
> 如果目標*表中不存在從表查詢中刪除的擴展區返回的擴展區*,則該命令將失敗。 如果在執行替換命令之前合併了擴展盤區,則可能發生此情況。 為了確保命令在缺少的擴展區上失敗,請檢查查詢是否返回預期的擴展盤Id。 如果表 MyOtherTable 中不存在下降範圍,則下面的示例#1將失敗。 但是,即使不存在下降範圍,示例#2也會成功,因為要刪除的查詢不會返回任何擴展盤 ID。 

範例#1: 

```kusto
.replace extents in table MyOtherTable <|
     { datatable(ExtentId:guid)[ "2cca5844-8f0d-454e-bdad-299e978be5df"] }, { .show table MyTable1 extents }
```

範例#2:

```kusto
.replace extents in table MyOtherTable  <|
     { .show table MyOtherTable extents | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) }, { .show table MyTable1 extents }
```



## <a name="drop-extent-tags"></a>.下降範圍標記

**語法**

`.drop`[`async` `extent` `table``(``,`*Tag1*`,` *TableName**Tag2*] 表名 ' 標籤1 ' ' ' 標籤2 ' ... `tags` `from``,`'*標籤*'*`)`

`.drop``async` `extent` [ `tags` ]*查詢* <| 

* `async`(可選)指定命令是否非同步執行(在這種情況下,將返回操作 ID (Guid),並且可以使用[.show 操作](operations.md#show-operations)命令監視操作的狀態)。
    * 如果使用此選項,可以檢索成功執行的結果[.show 操作詳細資訊](operations.md#show-operation-details)命令)。

這個指令在特定資料庫的上下文中執行,並從提供的資料庫和表中的任何範圍(包括任何標籤)中刪除提供的[延伸區標記](extents-overview.md#extent-tagging)。  

有兩種方法可以指定應從哪些擴展區中刪除哪些標記:

1. 通過顯式指定應從指定表中的所有擴展區中刪除的標記。
2. 通過提供其結果指定表和每個範圍中的範圍 ID 的查詢 - 應刪除的標記。

**限制**
- 所有擴展區都必須位於上下文資料庫中,並且必須屬於同一表。 

**使用查詢指定範圍**

需要所有相關來源與目標表的[表員權限](../management/access-control/role-based-authorization.md)。

```kusto 
.drop extent tags <| ...query... 
```

使用 Kusto 查詢指定要刪除的範圍和標記,該查詢傳回具有名為「天度Id」的欄和名為「標記」的欄的記錄集。 

*註*: 使用[Kusto .NET 用戶端函式庫](../api/netfx/about-kusto-data.md)時,可以使用以下方法產生所需的指令:
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**傳回輸出**

輸出參數 |類型 |描述 
---|---|---
原始範圍Id |字串 |標記已修改的原始範圍的唯一識別碼 (GUID),並作為操作的一部分刪除) 
結果範圍Id |字串 |具有修改標記(並作為操作的一部分創建和添加)的結果範圍的唯一標識符 (GUID)。 失敗後 - "失敗"
結果範圍標記 |字串 |結果範圍標記為標記的標記的集合(如果保留任何標記)或「null」,以防操作失敗。
詳細資料 |字串 |包括故障詳細資訊,以防操作失敗。

**範例**

從`drop-by:Partition000``MyOtherTable`表 中帶有標記的任何範圍中丟棄標記。

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

從表中`drop-by:20160810104500``My Table`標有`a random tag`其中 任何一個`drop-by:20160810`的標籤 範圍刪除標記 、和/或。

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

從表中`drop-by``MyTable`的擴展區刪除所有標記。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

`drop-by:StreamCreationTime_20160915(\d{6})`從表中`MyTable`的擴展區刪除匹配正則運算式的所有標記。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**範例輸出** 

|原始範圍Id |結果範圍Id | 結果範圍標記 | 詳細資料
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | 分割區001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | 分割區001 分區002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |

## <a name="alter-extent-tags"></a>.alter 範圍標記

**語法**

`.alter``async` `extent` [ `tags` `,``,`*Tag2*]*標籤1*' ' Tag2 ' ... `(``,` *'TagN']*`)` <| *查詢*

該命令在特定資料庫的上下文中運行,並將指定查詢返回的所有範圍的[擴展區標記](extents-overview.md#extent-tagging)更改為提供的標記集。

使用 Kusto 查詢指定要更改的範圍和標記,該查詢返回具有名為「特定程度的 Id」的列的記錄集。

* `async`(可選)指定命令是否非同步執行(在這種情況下,將返回操作 ID (Guid),並且可以使用[.show 操作](operations.md#show-operations)命令監視操作的狀態)。
    * 如果使用此選項,可以檢索成功執行的結果[.show 操作詳細資訊](operations.md#show-operation-details)命令)。

需要所有相片表的[表管理權限](../management/access-control/role-based-authorization.md)。

**限制**
- 所有擴展區都必須位於上下文資料庫中,並且必須屬於同一表。 

**傳回輸出**

輸出參數 |類型 |描述 
---|---|---
原始範圍Id |字串 |標記已修改的原始範圍的唯一識別碼 (GUID),並作為操作的一部分刪除) 
結果範圍Id |字串 |具有修改標記(並作為操作的一部分創建和添加)的結果範圍的唯一標識符 (GUID)。 失敗後 - "失敗"
結果範圍標記 |字串 |結果範圍標記的標記的集合,或操作失敗時"null"的標記的集合。
詳細資料 |字串 |包括故障詳細資訊,以防操作失敗。

**範例**

將表格`MyTable`中 所有延伸的分割分頁`MyTag`變更為 。

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

變更表中`MyTable`所有延伸盤區的分頁,標`drop-by:MyTag`記`drop-by:MyNewTag``MyOtherNewTag`為與 。

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**範例輸出** 

|原始範圍Id |結果範圍Id | 結果範圍標記 | 詳細資料
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | 下一個: 我的新分頁我的其他新分頁| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | 下一個: 我的新分頁我的其他新分頁| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | 下一個: 我的新分頁我的其他新分頁| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | 下一個: 我的新分頁我的其他新分頁| 

