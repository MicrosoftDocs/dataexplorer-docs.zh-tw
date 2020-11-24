---
title: Kusto 查詢內嵌 (設定、附加、取代) -Azure 資料總管
description: 本文說明如何從查詢 ( 內嵌。在 Azure 資料總管中設定、附加、設定或取代) 。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
ms.openlocfilehash: 18959e2387a1a0faf92261dc3c35eca0db44c158
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512888"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>從查詢 ( 內嵌。 set、. append、. set-或-append、set 或-replace) 

這些命令會執行查詢或控制命令，並將查詢的結果內嵌到資料表中。 這些命令之間的差異在於它們如何處理現有或不存在的資料表和資料。

|命令          |如果資料表存在                     |如果資料表不存在                    |
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

* `async`：如果有指定，命令會立即傳回並繼續在背景中內嵌。 命令的結果將包含 `OperationId` 可以搭配命令使用的值 `.show operations` ，以取得內嵌完成狀態和結果。
* *TableName*：要內嵌資料的資料表名稱。
  資料表名稱永遠與內容中的資料庫相關。
* *PropertyName*、 *PropertyValue*：會影響內嵌進程的任意數目的內嵌屬性。

 內嵌支援的屬性。

|屬性        |描述|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | ISO8601 字串格式的日期時間值，用於內嵌資料範圍的建立時間。 如果未指定，將會使用目前的值 (`now()`) |
|`extend_schema`  | 布林值，如果指定的話，會指示命令擴充資料表的架構。 預設值為 "false"。 此選項只適用于 `.append` 、 `.set-or-append` 和 `set-or-replace` 命令。 唯一允許的架構延伸會將其他資料行加入至資料表結尾|
|`recreate_schema`  | 布林值。 如果有指定，則描述命令是否可以重新建立資料表的架構。 預設值為 "false"。 此選項只適用于 *set 或 replace* 命令。 如果同時設定了這兩個選項，此選項的優先順序會高於 extend_schema 屬性|
|`folder`         | 要指派給資料表的資料夾。 如果資料表已經存在，這個屬性將會覆寫資料表的資料夾。|
|`ingestIfNotExists`   | 字串值。 如果有指定，如果資料表已經有標記 `ingest-by:` 具有相同值的標記，就會防止從後續內嵌|
|`policy_ingestiontime`   | 布林值。 如果有指定，描述是否要在這個命令所建立的資料表上啟用內嵌 [時間原則](../../management/ingestiontime-policy.md) 。 預設值為 "true"|
|`tags`   | JSON 字串，指出要在內嵌期間執行哪些驗證|
|`docstring`   | 記錄資料表的字串|

 控制命令列為的屬性。

|屬性        |類型    |描述|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |表示命令內嵌自所有執行查詢的節點。 預設值為 "false"。  請參閱下面的＜備註＞。|

* *QueryOrCommand*：查詢或控制命令的文字，其結果將用來當做要內嵌的資料。

> [!NOTE]
> 僅 `.show` 支援控制命令。

**備註**

* `.set-or-replace` 如果資料表存在，則取代資料表的資料。 它會卸載現有的資料分區或建立目標資料表（如果尚未存在）。
  除非其中一個 `extend_schema` 或內嵌 `recreate_schema` 屬性設定為 "true"，否則將保留資料表架構。 如果已修改架構，就會發生在其本身的交易中的實際資料內嵌之前。 內嵌資料失敗並不表示架構未修改。
* `.set-or-append``.append`除非 [內嵌 `extend_schema` ] 屬性設定為 "true"，否則命令將保留架構。 如果已修改架構，就會發生在其本身的交易中的實際資料內嵌之前。 內嵌資料失敗並不表示架構未修改。
* 建議您將資料內嵌的資料限制為每個內嵌作業小於 1 GB。 如有必要，可以使用多個內嵌命令。
* 資料內嵌是一項需要大量資源的作業，可能會影響叢集上的並行活動，包括執行查詢。 請避免同時執行太多這類命令。
* 當比對結果集架構與目標資料表的架構時，比較是以資料行類型為基礎。 沒有任何資料行名稱相符。 請確定查詢結果架構資料行的順序與資料表的順序相同。 否則資料會內嵌到錯誤的資料行。
* `distributed`當查詢所產生的資料量很大時，將旗標設為 "true" 會很有用，而且查詢不需要序列化，因此多個節點可以平行產生輸出。
  當查詢結果很小時，請勿使用此旗標，因為它可能會不必要地產生許多小型資料分區。

**範例** 

在資料庫中建立名為的新資料表，這個資料表的 :::no-loc text="RecentErrors"::: 架構與相同的架構 :::no-loc text="LogsTable"::: ，而且會保留最後一個小時的所有錯誤記錄。

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

在資料庫中建立名為 "OldExtents" 的新資料表（該資料表具有單一資料行 "ExtentId"），並保留在先前建立超過30天的資料庫中，所有範圍的範圍識別碼。 資料庫具有名為 "MyExtents" 的現有資料表。

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

在目前資料庫中，將資料附加至名為 "OldExtents" 的現有資料表中，該資料表具有單一資料行 "ExtentId"，並保留在過去30天內建立的資料庫中，所有範圍的範圍識別碼。
使用標籤 `tagA` 和 `tagB` ，以名為 "MyExtents" 的現有資料表為基礎，標記新的範圍。

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

將資料附加至目前資料庫中的 "OldExtents" 資料表，或建立資料表（如果尚未存在）。 使用標記新的範圍 `ingest-by:myTag` 。 只有在資料表尚未包含標記為的範圍時，才會以 `ingest-by:myTag` 名為 "MyExtents" 的現有資料表為基礎。

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

取代目前資料庫中 "OldExtents" 資料表內的資料，或建立資料表（如果尚未存在）。 使用標記新的範圍 `ingest-by:myTag` 。

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

將資料附加至目前資料庫中的 "OldExtents" 資料表，同時將建立的範圍 (s) 建立時間設定為過去的特定日期時間。

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**傳回輸出**
 
傳回由於或命令所建立之範圍的 `.set` 資訊 `.append` 。

**範例輸出**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |
