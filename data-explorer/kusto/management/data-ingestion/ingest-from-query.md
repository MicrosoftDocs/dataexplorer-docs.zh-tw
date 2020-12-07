---
title: Kusto 查詢內嵌 (set、append、replace) - Azure 資料總管
description: 本文說明在 Azure 資料總管中如何內嵌查詢中的資料 (.set、.append、.set-or-append、.set-or-replace)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
ms.openlocfilehash: 18959e2387a1a0faf92261dc3c35eca0db44c158
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512888"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>從查詢內嵌 (.set、.append、.set-or-append、.set-or-replace)

這些命令會執行查詢或控制命令，並將查詢的結果內嵌到資料表中。 這些命令的差異在於如何處理已存在或不存在的資料表和資料。

|命令          |如果資料表存在                     |如果資料表不存在                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |命令會失敗                  |建立資料表並內嵌資料|
|`.append`        |資料會附加至資料表      |命令會失敗                        |
|`.set-or-append` |資料會附加至資料表      |建立資料表並內嵌資料|
|`.set-or-replace`|資料會取代資料表中的資料|建立資料表並內嵌資料|

**語法**

`.set` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...`])`] `<|` *QueryOrCommand*

`.set-or-append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.set-or-replace` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

**引數**

* `async`：如果指定，命令會立即傳回並在背景中繼續內嵌。 命令的結果將會包含 `OperationId` 值，然後可以與 `.show operations` 命令搭配使用，以取得內嵌完成的狀態和結果。
* *TableName*：要將資料內嵌到其中的資料表名稱。
  資料表名稱一律與內容中的資料庫相關。
* *PropertyName*、*PropertyValue*：任何數目的內嵌屬性，這些屬性都會影響到內嵌程序。

 支援的內嵌屬性。

|屬性        |描述|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | 日期時間值 (格式為 ISO8601 字串)，在建立內嵌資料範圍的時間時使用。 如未指定，則會使用目前的值 (`now()`)|
|`extend_schema`  | 布林值，若已指定，則指示命令擴充資料表的結構描述。 預設值為 "false" 此選項僅適用於 `.append`、`.set-or-append` 和 `set-or-replace` 命令。 唯一允許的結構描述擴充會在資料表結尾新增其他資料行|
|`recreate_schema`  | 布林值。 如果指定，則會描述命令是否可以重新建立資料表的架構。 預設值為 "false" 此選項只適用於 set-or-replace 命令。 此選項的優先順序高於 extend_schema 屬性 (如果兩者都已設定)|
|`folder`         | 要指派給資料表的資料夾。 如果資料表已經存在，此屬性將會覆寫資料表的資料夾。|
|`ingestIfNotExists`   | 字串值。 如果指定，則會在資料表中已經有具相同值和 `ingest-by:` 標記的資料時，防止從後續內嵌|
|`policy_ingestiontime`   | 布林值。 如果指定，則描述是否要在此命令所建立的資料表上啟用[內嵌時間原則](../../management/ingestiontime-policy.md)。 預設值是 "true"|
|`tags`   | 指出要在內嵌期間執行哪些驗證的 JSON 字串|
|`docstring`   | 記錄資料表的字串|

 控制命令列行為的屬性。

|屬性        |類型    |描述|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |表示命令會從所有平行執行查詢的節點內嵌。 預設值為 "false"  請參閱下面的＜備註＞。|

* *QueryOrCommand*：查詢或控制命令的文字，其結果將用來作為要內嵌的資料。

> [!NOTE]
> 僅支援 `.show` 控制命令。

**備註**

* `.set-or-replace` 會取代資料表的資料 (如果有的話)。 其會捨棄現有的資料分區，或建立目標資料表 (如果尚未存在的話)。
  除非 `extend_schema` 或 `recreate_schema` 的其中一個內嵌屬性設定為 "true"，否則會保留資料表架構。 如果修改架構，則會在其本身交易中內嵌實際資料之前發生。 無法內嵌資料並不表示架構並未修改。
* 除非 `extend_schema` 內嵌屬性設定為 "true"，否則 `.set-or-append` 和 `.append` 命令會保留架構。 如果修改架構，則會在其本身交易中內嵌實際資料之前發生。 無法內嵌資料並不表示架構並未修改。
* 我們建議您針對每個內嵌作業，將內嵌的資料限制為少於 1 GB。 如有需要，可以使用多個內嵌命令。
* 資料內嵌是需要大量資源的作業，因此可能會影響叢集上的並行活動，包括執行查詢。 請避免同時執行太多這類命令。
* 比對結果集架構與目標資料表時，比較是以資料行類型作為基礎。 沒有相符的資料行名稱。 請確定查詢結果架構資料行與資料表的順序相同。 否則，資料將會內嵌至錯誤的資料行。
* 當查詢所產生的資料量很大 (超過 1GB)，而且查詢不需要序列化時，將 `distributed` 旗標設定為 "true" 會很有用，如此可讓多個節點平行地產生輸出。
  當查詢結果很小時，請勿使用此旗標，因為其可能會不必要地產生許多小型資料分區。

**範例** 

在資料庫中建立名為 :::no-loc text="RecentErrors"::: 的新資料表，其架構與 :::no-loc text="LogsTable"::: 相同且保留過去一小時的所有錯誤記錄。

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

在資料庫中建立名為 "OldExtents" 的新資料表，其具有單一資料行 "ExtentId"，並保留資料庫中已建立超過 30 天的所有範圍的範圍識別碼。 資料庫具有名為 "MyExtents" 的現有資料表。

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

在目前資料庫中的 "OldExtents" 資料表中附加資料，其具有單一資料行 "ExtentId"，並保留資料庫中已建立超過 30 天的所有範圍的範圍識別碼。
以名為 "MyExtents" 的現有資料表為基礎，使用 `tagA` 和 `tagB` 標記來標示新的範圍。

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

將資料附加至目前資料庫中的 "OldExtents" 資料表，或建立資料表 (如果尚未存在的話)。 使用 `ingest-by:myTag` 標記新的範圍。 只有在資料表尚未包含以 `ingest-by:myTag` 標記的範圍時，才執行此動作 (以名為 "MyExtents" 的現有資料表為基礎)。

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

取代目前資料庫中 "OldExtents" 資料表的資料，或建立資料表 (如果尚未存在的話)。 使用 `ingest-by:myTag` 標記新的範圍。

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

將資料附加至目前資料庫中的 "OldExtents" 資料表，同時將已建立範圍的建立時間設定為過去的特定日期時間。

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**傳回輸出**
 
傳回因為 `.set` 或 `.append` 命令所建立的範圍相關資訊。

**範例輸出**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |
