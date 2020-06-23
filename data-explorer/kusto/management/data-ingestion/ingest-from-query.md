---
title: Kusto 查詢內嵌（設定、附加、取代）-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中從查詢（. set、. append、. set-或-append、. set-或-replace）內嵌。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 5248b9d986845ff7f35085cef0100cf3ab4b90da
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264465"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>從查詢內嵌（. set、. append、. set-或-append、. set-或-replace）

這些命令會執行查詢或控制命令，並將查詢的結果內嵌到資料表中。 這些命令的差異在於它們如何處理現有或不存在的資料表和資料。

|Command          |如果資料表存在                     |如果資料表不存在                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |命令失敗                  |建立資料表並內嵌資料|
|`.append`        |資料會附加至資料表      |命令失敗                        |
|`.set-or-append` |資料會附加至資料表      |建立資料表並內嵌資料|
|`.set-or-replace`|資料會取代資料表中的資料|建立資料表並內嵌資料|

**語法**

`.set`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*QueryOrCommand*

`.append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ... `])` ] `<|`*QueryOrCommand*

`.set-or-append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*QueryOrCommand*

`.set-or-replace`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*QueryOrCommand*

**引數**

* `async`：如果指定，此命令會立即傳回並繼續在背景中內嵌。 命令的結果會包含一個 `OperationId` 值，之後可以搭配 `.show operations` 命令使用，以取得內嵌完成狀態和結果。
* *TableName*：要內嵌資料的資料表名稱。
  資料表名稱一律與內容中的資料庫相關。
* *PropertyName*、 *PropertyValue*：任何數目的內嵌屬性會影響內嵌進程。

 內嵌支援的屬性。

|屬性        |描述|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | ISO8601 字串格式的日期時間值，用於內嵌資料範圍的建立時間。 如果未指定，則會使用目前的值（ `now()` ）|
|`extend_schema`  | 布林值，如果指定，則指示命令擴充資料表的架構。 預設值為 "false"。 此選項只適用于 `.append` 、 `.set-or-append` 和 `set-or-replace` 命令。 唯一允許的架構延伸模組會在結尾處加入資料表中的其他資料行|
|`recreate_schema`  | 布林值。 如果有指定，則會描述命令是否可以重新建立資料表的架構。 預設值為 "false"。 此選項只適用于 [*設定] 或 [取代*] 命令。 此選項的優先順序高於 extend_schema 屬性（如果兩者都已設定）|
|`folder`         | 要指派給資料表的資料夾。 如果資料表已經存在，這個屬性將會覆寫資料表的資料夾。|
|`ingestIfNotExists`   | 字串值。 若已指定，則在資料表已經有標記有相同值的標記的資料時，防止從後續內嵌 `ingest-by:`|
|`policy_ingestiontime`   | 布林值。 如果指定，描述是否要在此命令所建立的資料表上啟用「內嵌[時間原則](../../management/ingestiontime-policy.md)」。 預設值為 "true"|
|`tags`   | JSON 字串，指出要在內嵌期間執行哪一種驗證|
|`docstring`   | 記錄資料表的字串|

 控制命令列為的屬性。

|屬性        |類型    |Description|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |表示命令會從所有執行平行查詢的節點內嵌。 預設值為 "false"。  請參閱下面的備註。|

* *QueryOrCommand*：查詢或控制項命令的文字，其結果將做為要內嵌的資料。

> [!NOTE]
> 僅 `.show` 支援控制命令。

**備註**

* `.set-or-replace`取代資料表的資料（如果有的話）。 它會卸載現有的資料分區，或建立目標資料表（如果尚未存在的話）。
  除非其中一個 `extend_schema` 或內嵌 `recreate_schema` 屬性設定為 "true"，否則會保留資料表架構。 如果已修改架構，則會在實際資料內嵌于其本身的交易之前發生。 無法內嵌資料，並不表示架構並未修改。
* `.set-or-append`除非將內嵌 `.append` `extend_schema` 屬性設定為 "true"，否則和命令會保留架構。 如果已修改架構，則會在實際資料內嵌于其本身的交易之前發生。 無法內嵌資料，並不表示架構並未修改。
* 我們建議您針對每個內嵌作業，將內嵌的資料限制為少於 1 GB。 如有需要，可以使用多個內嵌命令。
* 資料內嵌是需要大量資源的作業，可能會影響叢集上的並行活動，包括執行查詢。 請避免同時執行太多這類命令。
* 當比對結果集架構與目標資料表時，比較是以資料行類型為基礎。 沒有相符的資料行名稱。 請確定查詢結果架構資料行與資料表的順序相同。 否則，資料將會內嵌至錯誤的資料行。
* `distributed`當查詢所產生的資料量很大時，將旗標設為 "true" 會很有用，而且查詢不需要序列化，因此多個節點可以平行產生輸出。
  當查詢結果很小時，請勿使用此旗標，因為它可能會不必要地產生許多小型資料分區。

**範例** 

在資料庫中建立名為 "RecentErrors" 的新資料表，其架構與 "LogsTable" 相同，並保留過去一小時的所有錯誤記錄。

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

在資料庫中建立名為 "OldExtents" 的新資料表，其具有單一資料行 "ExtentId"，並保留資料庫中已建立超過30天的所有範圍的範圍識別碼。 資料庫具有名為 "MyExtents" 的現有資料表。

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

在目前資料庫中，將資料附加到名為 "OldExtents" 的現有資料表中，其具有單一資料行 "ExtentId"，並保留資料庫中已建立超過30天的所有範圍的範圍識別碼。
`tagA` `tagB` 根據名為 "MyExtents" 的現有資料表，使用標籤和來標記新的範圍。

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

將資料附加至目前資料庫中的 "OldExtents" 資料表，或建立資料表（如果尚未存在的話）。 使用標記新的範圍 `ingest-by:myTag` 。 只有在資料表尚未包含以 `ingest-by:myTag` 名為 "MyExtents" 的現有資料表為基礎的範圍時，才執行此動作。

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

取代目前資料庫中 "OldExtents" 資料表內的資料，或建立資料表（如果尚未存在的話）。 使用標記新的範圍 `ingest-by:myTag` 。

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

將資料附加至目前資料庫中的 "OldExtents" 資料表，同時將建立的範圍建立時間設定為過去的特定日期時間。

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**傳回輸出**
 
傳回因或命令而建立之範圍的 `.set` 資訊 `.append` 。

**範例輸出**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |
