---
title: 從查詢(.set,.附加,.set 或追加,.set 或替換) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理員中的查詢(設置、.追加、.set 或追加、.set 或替換)中的引入。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: fb0bbb06bb8d28dd3951a7faedf55b0d84155301
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521449"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>從查詢中引入(.set、.附加、.set 或追加、.set 或取代)

這些命令執行查詢或控件命令,並將查詢的結果引入表中。 這些命令之間的區別在於它們如何處理現有或不存在的表和數據:

|Command          |如果表存在                     |如果表不存在                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |命令失敗。                  |將創建該表並引入數據。|
|`.append`        |數據將追加到表中。      |命令失敗。                        |
|`.set-or-append` |數據將追加到表中。      |將創建該表並引入數據。|
|`.set-or-replace`|數據替換表中的數據。|將創建該表並引入數據。|

**語法**

`.set`[`async`]*表名*`with``(`=*屬性名稱*`=`*屬性值*[`,` ...`)`• `<|` *查詢Or命令*

`.append`[`async`]*表名*`with``(`=*屬性名稱*`=`*屬性值*=`,` ...`])`• `<|` *查詢Or命令*

`.set-or-append`[`async`]*表名*`with``(`=*屬性名稱*`=`*屬性值*[`,` ...`)`• `<|` *查詢Or命令*

`.set-or-replace`[`async`]*表名*`with``(`=*屬性名稱*`=`*屬性值*[`,` ...`)`• `<|` *查詢Or命令*

**引數**

* `async`:如果指定,該命令將立即返回,並繼續在後台引入。 該命令的結果將包括一個`OperationId`值,該值隨後可用於該`.show operation`命令以檢索引入完成狀態和結果。
* *表名稱*:要將資料引入到的表的名稱。
  表名稱始終相對於上下文中的資料庫。
* *屬性名稱*,*屬性值*:影響引入過程的任意數量的引入屬性。

 支援的引入屬性:

|屬性        |描述|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | 在引入的數據範圍的創建時要使用的日期時間值(格式化為 ISO8601 字串)。 如果未指定,將使用當前值(現在)。|
|`extend_schema`  | 布爾值(如果指定)指示命令擴展表的架構(預設值為 false)。 此選項僅適用於 .append .set 或附加命令以及設置或替換命令。 唯一允許的結構描述擴充會在資料表結尾新增其他資料行。|
|`recreate_schema`  | 布爾值(如果指定)描述該命令是否可以重新創建表的架構(預設值為 false)。 此選項僅適用於設定或替換命令。 如果同時設置了extend_schema屬性,則此選項優先於extend_schema屬性。|
|`folder`         | 要分配給表的資料夾。 如果表已存在,則此屬性將覆蓋表的資料夾。|
|`ingestIfNotExists`   | 如果指定,如果表已使用引入:標記具有相同值,則防止引入成功的字串值。|
|`policy_ingestiontime`   | 布林值，若已指定，則描述是否要在此命令所建立的資料表上啟用[內嵌時間](../../management/ingestiontime-policy.md)原則。 預設值是 true。|
|`tags`   | 指出要在內嵌期間執行哪些驗證的 JSON 字串。|
|`docstring`   | 記錄表的字串。|

  此外,還有一個屬性控制命令本身的行為:

|屬性        |類型    |描述|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |指示命令從並行執行查詢的所有節點中引入。 ( 預設值`false`為 .) 請參閱下面的備註。|

* *查詢Or命令*:查詢或控制命令的文本,其結果將用作要攝用的數據。

> [!NOTE]
> 僅`.show`支援控制命令。

**備註**

* `.set-or-replace`如果表存在(刪除現有數據分片),則替換表的數據,如果不存在,則替換目標表。
  除非將`extend_schema``recreate_schema`表 架構`true`設定為, 否則將保留表架構。 如果修改了架構,這將發生在其自己的事務中的實際數據引入之前,因此無法引入數據並不意味著未修改架構。
* `.set-or-append`與`.append`指令保留架構,除非`extend_schema`引入屬性設定為`true`。 如果修改了架構,這將發生在其自己的事務中的實際數據引入之前,因此無法引入數據並不意味著未修改架構。
* **強烈建議每次**引入操作的數據限制為小於 1 GB。 如有必要,可以使用多個引入命令。
* 數據引入是一項資源密集型操作,可能會影響群集上的併發活動,包括運行查詢。 避免同時同時運行「太多」 的此類命令。
* 將結果集架構與目標表的架構匹配時,比較基於列類型。 列名稱沒有匹配,因此請確保查詢結果架構列的順序與表相同,否則數據將被引入錯誤的列。
* 當查詢`distributed`生成`true`的數據量較大(超過 1GB 的數據)**並且**查詢不需要序列化(以便多個節點可以並行生成輸出)時,將標誌設置為,非常有用。
  當查詢結果較小時,不建議使用此標誌,因為它可能會不必要地生成大量小數據碎片。

**範例** 

在目前資料庫中建立名為「最近錯誤」的新表,該表的架構與「LogsTable」具有相同的架構,並保存最後一小時的所有錯誤記錄:

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

在目前的資料庫中建立名為「OldAds」的新表,該表具有單個列(「天度Id」),並基於名為「MyA0s」的現有表保存資料庫中 30 天前創建的所有擴展盤區 ID:

```kusto
.set async OldExtents <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

將資料追加到目前資料庫中名為"OldEds"的現有表,該表具有單個列("天度Id"),並保存資料庫中 30 多天前創建的所有擴展盤區的範圍 ID,同時根據`tagA``tagB`名為"MyAs"的現有表標記新擴展盤:

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```
 
將資料追加到當前資料庫中的"OldA."表(如果表不存在,則創建該表),同時使用`ingest-by:myTag`標記新擴展盤區。 僅當表尚未包含標記為`ingest-by:myTag`的擴展盤區時,才基於名為「MyA0s」的現有表, 執行此操作:

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

替換目前資料庫中的"OldA0s"表中的數據(如果表不存在,則創建該表),同時使用`ingest-by:myTag`標記新擴展區。

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

將資料附加到目前的資料庫中的「OldA.s」表,同時將建立的延伸區建立時間設定為過去的特定日期時間:

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**傳回輸出**
 
返回有關`.set`由於`.append`或命令而創建的擴展盤區的資訊。

**範例輸出**

|放大縮小字型功能 放大縮小字型功能 |原始大小 |範圍大小 |壓縮大小 |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |