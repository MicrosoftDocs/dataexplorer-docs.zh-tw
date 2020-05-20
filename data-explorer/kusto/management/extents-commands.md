---
title: 範圍（資料分區）管理-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的範圍（資料分區）管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca66beaad41796dff38a5bd5ce1fad2e0f41fc72
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550498"
---
# <a name="extents-data-shards-management"></a>範圍（資料分區）管理

資料分區在 Kusto 中稱為**範圍**，而所有命令都會使用「範圍」或「範圍」做為同義字。

## <a name="show-extents"></a>。顯示範圍

**叢集層級**

`.show` `cluster` `extents` [`hot`]

顯示存在於叢集中的範圍（資料分區）的相關資訊。
如果 `hot` 指定，則只會顯示預期在熱快取中的範圍。

**資料庫層級**

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ... `,`*ExtentIdN* `)` ][ `hot` ] [ `where` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag1* [ `and` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag2*...]]

顯示存在於指定資料庫中的範圍（資料分區）的相關資訊。
如果 `hot` 指定，則只會顯示預期在熱快取中的範圍。

**資料表層級**

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ... `,`*ExtentIdN* `)` ][ `hot` ] [ `where` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag1* [ `and` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag2*...]]

`.show``tables` `(` *TableName1* `,` ... `,`*TableNameN* `)``extents`[ `(` *ExtentId1* `,` ... `,`*ExtentIdN* `)` ][ `hot` ] [ `where` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag1* [ `and` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag2*...]]

顯示指定資料表中所存在之範圍（資料分區）的相關資訊（從命令的內容取得資料庫）。
如果 `hot` 指定，則只會顯示預期在熱快取中的範圍。

**注意：**

* 使用資料庫和/或資料表層級命令比篩選（新增）叢集 `| where DatabaseName == '...' and TableName == '...'` 層級命令的結果快很多。
* 如果提供了選擇性的範圍識別碼清單，則傳回的資料集會限制為僅限這些範圍。
    * 同樣地，這比篩選（新增 `| where ExtentId in(...)` 至）「裸機」命令的結果快很多。
* 在 `tags` 指定篩選準則時：
  * 傳回的清單僅限於標記集合遵守*所有*提供的標記篩選準則的範圍。
    * 同樣地，這比篩選（新增）「 `| where Tags has '...' and Tags contains '...'` 裸機」命令的結果快很多。
  * `has`篩選是相等的篩選準則：未標記任何指定標記的範圍會被篩選掉。
  * `!has`篩選是相等的負面篩選準則：將會篩選出使用其中一個指定標記標記的範圍。
  * `contains`篩選器是不區分大小寫的子字串篩選準則：不含指定字串做為其任何標記之子字串的範圍會被篩選掉。 
  * `!contains`篩選是不區分大小寫的子字串負篩選：具有指定字串做為其任何標記之子字串的範圍會被篩選掉。 
  
  * **範例**
    * `E`資料表中的範圍 `T` 會加 `aaa` 上標記、 `BBB` 和 `ccc` 。
    * 此查詢將會傳回 `E` ： 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * 此查詢*不*會傳回 `E` （因為它不是以等於的標記標記 `aa` ）：
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * 此查詢將會傳回 `E` ： 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|輸出參數 |類型 |說明 
|---|---|---
|ExtentId |Guid |範圍的識別碼 
|DatabaseName |String |範圍所屬的資料庫 
|TableName |String |範圍所屬的資料表 
|MaxCreatedOn |Datetime |建立範圍的日期-時間（針對合併的範圍-來源範圍內的建立時間上限） 
|OriginalSize |Double |範圍資料的原始大小（以位元組為單位） 
|ExtentSize |Double |記憶體中的範圍大小（已壓縮 + 索引） 
|CompressedSize |Double |記憶體中的範圍資料的壓縮大小 
|IndexSize |Double |範圍資料的索引大小 
|區塊 |long |範圍中的資料區塊數量 
|使用者分佈 |long |範圍中的資料區段數量 
|AssignedDataNodes |String | 已淘汰（空字串）
|LoadedDataNodes |String |已淘汰（空字串）
|ExtentContainerId |String | 範圍所在的範圍容器識別碼
|RowCount |long |範圍中的資料列數量
|MinCreatedOn |Datetime |建立範圍的日期-時間（針對合併的範圍-來源範圍中的最小建立時間） 
|標籤|String|針對範圍定義的標記（如果有的話） 
 
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
 
## <a name="merge-extents"></a>。合併範圍

**語法**

`.merge``[async | dryrun]` *TableName* `(` *GUID1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

此命令會合並指定之資料表中其識別碼所指出的範圍（請參閱：[範圍（資料分區）總覽](extents-overview.md)）。

合併作業有2個類別： [*合併*] （重建索引）和 [*重建*] （完全 reingests 資料）。

* `async`：作業將會以非同步方式執行，結果將會是作業識別碼（GUID），它可以 `.show operations <operation ID>` 用來執行，以查看命令的狀態 & 狀態。
* `dryrun`：作業只會列出應該合併的範圍，但不會實際合併它們。 
* `with(rebuild=true)`：將會重建範圍（將會 reingested 資料），而不是合併（將會重建索引）。

**傳回輸出**

輸出參數 |類型 |說明 
---|---|---
OriginalExtentId |字串 |來源資料表中原始範圍的唯一識別碼（GUID），已合併至目標範圍。 
ResultExtentId |字串 |已從來源範圍建立之結果範圍的唯一識別碼（GUID）。 失敗時-「失敗」或「已放棄」。
Duration |時間範圍 |完成合併作業所花費的時間週期。

**範例**

重建中的兩個特定範圍 `MyTable` ，以非同步方式執行
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

合併中的兩個特定範圍 `MyTable` ，以同步方式執行
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**注意事項**
- 一般來說， `.merge` 命令應該不會以手動方式執行，而且會在 Kusto 叢集的背景中自動進行持續（根據針對資料表和資料庫所定義的[合併原則](mergepolicy.md)）。  
  - 將多個範圍合併成單一範圍之準則的一般考慮記載于 [[合併原則](mergepolicy.md)] 底下。
- `.merge`作業有可能的最終狀態（以作業 `Abandoned` 識別碼執行時可能會看到 `.show operations` ）-此狀態表示來源範圍並未合併在一起，因為部分來源範圍已不存在於來源資料表中。
  - 這類狀態預期會發生在下列情況：
     - 已卸載來源範圍做為資料表保留期的一部分。
     - 來源範圍已移至不同的資料表。
     - 來源資料表已完全卸載或重新命名。

## <a name="move-extents"></a>。移動範圍

**語法**

`.move`[ `async` ] `extents` `all` `from` `table` *SourceTableName* `to` `table` *DestinationTableName*

`.move`[ `async` ] `extents` `(` *GUID1* [ `,` *GUID2* ... `)` `from` ] `table`*SourceTableName* `to``table` *DestinationTableName* 

`.move`[ `async` ] `extents` `to` `table` *DestinationTableName*  <|  *查詢*

此命令會在特定資料庫的內容中執行，並以交易方式將指定的範圍從來源資料表移到目的地資料表。
需要來源和目的地資料表的[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

* `async`（選擇性）指定是否要以非同步方式執行命令（在這種情況下，會傳回作業識別碼（Guid），而且可以使用[. show operations](operations.md#show-operations)命令來監視操作的狀態）。
    * 如果使用此選項，則可以抓取成功執行的結果[。顯示作業詳細資料](operations.md#show-operation-details)命令）。

有三種方式可指定要移動的範圍：
1. 要移動特定資料表的所有範圍。
2. 藉由在來源資料表中明確指定範圍識別碼。
3. 藉由提供查詢，其結果會指定來源資料表中的範圍識別碼。

**限制**
- 來源和目的地資料表都必須在內容資料庫中。 
- 來源資料表中的所有資料行都應該存在於具有相同名稱和資料類型的目的地資料表中。

**使用查詢指定範圍**

```kusto 
.move extents to table TableName <| ...query... 
```

範圍的指定方式是使用 Kusto 查詢，其會傳回具有名為 "ExtentId" 之資料行的記錄集。 

傳回**輸出**（用於同步執行）

輸出參數 |類型 |說明 
---|---|---
OriginalExtentId |字串 |來源資料表中原始範圍的唯一識別碼（GUID），已移至目的地資料表。 
ResultExtentId |字串 |結果範圍的唯一識別碼（GUID），已從來源資料表移到目的地資料表。 失敗時-「失敗」。
詳細資料 |字串 |包含失敗詳細資料，以防作業失敗。

**範例**

將資料表中的所有範圍移 `MyTable` 到資料表 `MyOtherTable` 。
```kusto
.move extents all from table MyTable to table MyOtherTable
```

將2個特定範圍（依據其範圍識別碼）從資料表移動 `MyTable` 到資料表 `MyOtherTable` 。
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

將2個特定資料表（，）的所有範圍移 `MyTable1` `MyTable2` 至資料表 `MyOtherTable` 。
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**範例輸出** 

|OriginalExtentId |ResultExtentId| 詳細資料
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

## <a name="drop-extents"></a>。放置範圍

從指定的資料庫/資料表中卸載範圍。 此命令有數個變體：在一個變異中，要卸載的範圍是由 Kusto 查詢所指定，而在其他 variant 範圍中則是使用以下所述的迷你語言來指定。 
 
### <a name="specifying-extents-with-a-query"></a>使用查詢指定範圍

需要具有提供的查詢所傳回之範圍之資料表的[資料表管理員許可權](../management/access-control/role-based-authorization.md)foreach。

捨棄範圍（或只在使用時才會進行報告 `whatif` ）：

**語法**

`.drop``extents`[ `whatif` ] <|*查詢*

範圍的指定方式是使用 Kusto 查詢，其會傳回具有名為 "ExtentId" 之資料行的記錄集。 
 
### <a name="dropping-a-specific-extent"></a>卸載特定範圍

如果指定資料表名稱，則需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

如果未指定資料表名稱，則需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)。

**語法**

`.drop``extent` *ExtentId* [ `from` *TableName*]

### <a name="dropping-specific-multiple-extents"></a>卸載特定的多個範圍

如果指定資料表名稱，則需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

如果未指定資料表名稱，則需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)。

**語法**

`.drop``extents` `(` *ExtentId1* `,` .。。*ExtentIdN* `)`[ `from` *TableName*]

### <a name="specifying-extents-by-properties"></a>依屬性指定範圍

如果指定資料表名稱，則需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

如果未指定資料表名稱，則需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)。

`.drop``extents`[ `older` *N* （ `days`  |  `hours` ）] `from` （*TableName*  |  `all` `tables` ） [ `trim` `by` （ `extentsize`  |  `datasize` ） *N* （ `MB`  |  `GB`  |  `bytes` ）] [ `limit` *LimitCount*]

* `older`：只會捨棄超過*N*天/小時的範圍。 
* `trim`：作業將會修剪資料庫中的資料，直到範圍總和符合所需的大小（MaxSize） 
* `limit`：作業將會套用至第一個*LimitCount*範圍 

命令支援模擬（ `.drop-pretend` 而不是 `.drop` ）模式，它會產生輸出，如同命令會執行，但未實際執行。

**範例**

從資料庫的所有資料表中移除超過10天前建立的所有範圍`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

移除資料表和中的所有範圍 `Table1` `Table2` ，其建立時間超過10天前：

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

模擬模式：顯示命令所要移除的範圍（範圍識別碼參數不適用於此命令）：

```kusto
.drop-pretend extents older 10 days from all tables
```

從 ' TestTable ' 移除所有範圍：

```kusto
.drop extents from TestTable
```
 
**傳回輸出**

|輸出參數 |類型 |說明 
|---|---|---
|ExtentId |String |因命令而卸載的 ExtentId 
|TableName |String |資料表名稱，其中的範圍所屬  
|CreatedOn |Datetime |保存首次建立範圍的相關資訊的時間戳記 
 
**範例輸出** 

|範圍識別碼 |資料表名稱 |建立於 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12：48：49.4298178 

## <a name="replace-extents"></a>。取代範圍

**語法**

`.replace`[ `async` ] `extents` `in` `table` *DestinationTableName*要 `<| 
{` *從資料表查詢中卸載的範圍查詢*，以 `},{` *將範圍移至資料表*`}`

此命令會在特定資料庫的內容中執行、將指定的範圍從其來源資料表移至目的地資料表，並從目的地資料表中卸載指定的範圍。
所有的 drop 和 move 作業都是在單一交易中完成。

需要來源和目的地資料表的[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

* `async`（選擇性）指定是否要以非同步方式執行命令（在這種情況下，會傳回作業識別碼（Guid），而且可以使用[. show operations](operations.md#show-operations)命令來監視操作的狀態）。
    * 如果使用此選項，則可以抓取成功執行的結果[。顯示作業詳細資料](operations.md#show-operation-details)命令）。

藉由提供2個查詢來指定應卸載或移動的範圍
- *查詢要從資料表*卸載的範圍-此查詢的結果會指定範圍識別碼  
應該從目的地資料表中卸載。
- *查詢要移到資料表的範圍*-此查詢的結果會指定來源資料表中應該移至目的地資料表的範圍識別碼。

這兩個查詢都應該傳回具有名為 "ExtentId" 之資料行的記錄集。

**限制**
- 來源和目的地資料表都必須在內容資料庫中。 
- 查詢所指定*要從資料表中卸載之範圍*的所有範圍，都應該屬於目的地資料表。
- 來源資料表中的所有資料行都應該存在於具有相同名稱和資料類型的目的地資料表中。

傳回**輸出**（用於同步執行）

輸出參數 |類型 |說明 
---|---|---
OriginalExtentId |字串 |來源資料表中原始範圍的唯一識別碼（GUID），已移至目的地資料表，或已卸載之目的地資料表中的範圍。
ResultExtentId |字串 |已從來源資料表移到目的地資料表的結果範圍唯一識別碼（GUID），如果已從目的地資料表卸載範圍，則為-empty。 失敗時-「失敗」。
詳細資料 |字串 |包含失敗詳細資料，以防作業失敗。

> [!NOTE]
> 如果*要從資料表*查詢中卸載的範圍所傳回的範圍不存在於目的地資料表中，此命令將會失敗。 如果在執行 replace 命令之前合併範圍，就可能會發生這種情況。 若要確保命令在遺失的範圍中失敗，請檢查查詢是否傳回預期的 ExtentIds。 如果要卸載的範圍不存在於資料表 MyOtherTable 中，以下的範例 #1 將會失敗。 不過，#2 的範例會成功，即使要卸載的範圍不存在，因為卸載的查詢不會傳回任何範圍識別碼。 

**範例**

下列命令會將2個特定資料表（，）中的所有範圍移 `MyTable1` `MyTable2` 至資料表 `MyOtherTable` ，並卸載 `MyOtherTable` 標記為的所有範圍 `drop-by:MyTag` ：

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

**範例輸出** 

|OriginalExtentId |ResultExtentId |詳細資料
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 


下列命令會將1個特定資料表（）的所有範圍移 `MyTable1` 至資料表 `MyOtherTable` ，並依其識別碼在中卸載特定範圍 `MyOtherTable` ：


```kusto
.replace extents in table MyOtherTable <|
    {
        print ExtentId = "2cca5844-8f0d-454e-bdad-299e978be5df"
    },
    {
        .show table MyTable1 extents 
    }
```

```kusto
.replace extents in table MyOtherTable  <|
    {
        .show table MyOtherTable extents
        | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) 
    },
    {
        .show table MyTable1 extents 
    }
```

下列命令會執行等冪邏輯，因此它只會從資料表中卸載範圍， `t_dest` 以防有範圍要從資料表移 `t_source` 到資料表 `t_dest` ：

```kusto
.replace async extents in table t_dest <|
{
    let any_extents_to_move = toscalar( 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize count() > 0
    );
    let extents_to_drop =
        t_dest
        | where any_extents_to_move and extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_drop
},
{
    let extents_to_move = 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_move
}
```

## <a name="drop-extent-tags"></a>。放置範圍標記

**語法**

`.drop`[ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` '*TagN*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *查詢*

* `async`（選擇性）指定是否要以非同步方式執行命令（在這種情況下，會傳回作業識別碼（Guid），而且可以使用[. show operations](operations.md#show-operations)命令來監視操作的狀態）。
    * 如果使用此選項，則可以抓取成功執行的結果[。顯示作業詳細資料](operations.md#show-operation-details)命令）。

命令會在特定資料庫的內容中執行，並從提供的資料庫和資料表中的任何範圍卸載提供的[範圍標記](extents-overview.md#extent-tagging)，其中包括任何標記。  

有兩種方式可指定應該從哪些範圍移除哪些標記：

1. 藉由明確指定應該從指定資料表中的所有範圍移除的標記。
2. 藉由提供查詢，其結果會指定資料表中的範圍識別碼和 foreach 範圍（應移除的標記）。

**限制**
- 所有範圍都必須在內容資料庫中，而且必須屬於相同的資料表。 

**使用查詢指定範圍**

需要所有相關來源和目的地資料表的[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

```kusto 
.drop extent tags <| ...query... 
```

您可以使用 Kusto 查詢來指定要卸載的範圍和標籤，以傳回具有名為 "ExtentId" 之資料行的記錄集，以及名為 "Tags" 的資料行。 

*注意*：使用[Kusto .net 用戶端程式庫](../api/netfx/about-kusto-data.md)時，您可以使用下列方法來產生所需的命令：
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**傳回輸出**

輸出參數 |類型 |說明 
---|---|---
OriginalExtentId |字串 |原始範圍的唯一識別碼（GUID），其標記已修改（並在作業中卸載） 
ResultExtentId |字串 |具有已修改標記之結果範圍的唯一識別碼（GUID）（並會在作業中建立和新增）。 失敗時-「失敗」。
ResultExtentTags |字串 |標記的集合，其中的結果範圍會加上標籤（如果有的話）或 "null" （如果作業失敗的話）。
詳細資料 |字串 |包含失敗詳細資料，以防作業失敗。

**範例**

`drop-by:Partition000`從資料表中標記為它的任何範圍卸載標記 `MyOtherTable` 。

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

`drop-by:20160810104500` `a random tag` `drop-by:20160810` 從資料表中任何已標記的範圍中，卸載標記、和/或 `My Table` 。

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

`drop-by`從資料表的範圍中卸載所有標記 `MyTable` 。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

從資料表的範圍中卸載符合 RegEx 的所有標記 `drop-by:StreamCreationTime_20160915(\d{6})` `MyTable` 。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**範例輸出** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | 詳細資料
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |

## <a name="alter-extent-tags"></a>。 alter 範圍標記

**語法**

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` '*TagN*'] `)`  <|  *查詢*

此命令會在特定資料庫的內容中執行，並將指定查詢所傳回之所有範圍的[範圍標記](extents-overview.md#extent-tagging)變更為提供的標記集。

您可以使用 Kusto 查詢來指定要改變的範圍和標籤，以傳回具有名為 "ExtentId" 之資料行的記錄集。

* `async`（選擇性）指定是否要以非同步方式執行命令（在這種情況下，會傳回作業識別碼（Guid），而且可以使用[. show operations](operations.md#show-operations)命令來監視操作的狀態）。
    * 如果使用此選項，則可以抓取成功執行的結果[。顯示作業詳細資料](operations.md#show-operation-details)命令）。

需要所有相關資料表的[資料表管理員許可權](../management/access-control/role-based-authorization.md)。

**限制**
- 所有範圍都必須在內容資料庫中，而且必須屬於相同的資料表。 

**傳回輸出**

輸出參數 |類型 |說明 
---|---|---
OriginalExtentId |字串 |原始範圍的唯一識別碼（GUID），其標記已修改（並在作業中卸載） 
ResultExtentId |字串 |具有已修改標記之結果範圍的唯一識別碼（GUID）（並會在作業中建立和新增）。 失敗時-「失敗」。
ResultExtentTags |字串 |已標記結果範圍的標記集合，如果作業失敗則為 "null"。
詳細資料 |字串 |包含失敗詳細資料，以防作業失敗。

**範例**

將資料表中所有範圍的標記變更 `MyTable` 為 `MyTag` 。

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

改變數據表中所有範圍的標記 `MyTable` ， `drop-by:MyTag` 並將其標記為 `drop-by:MyNewTag` 和 `MyOtherNewTag` 。

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**範例輸出** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | 詳細資料
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | drop by： MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | drop by： MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | drop by： MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | drop by： MyNewTag MyOtherNewTag| 

