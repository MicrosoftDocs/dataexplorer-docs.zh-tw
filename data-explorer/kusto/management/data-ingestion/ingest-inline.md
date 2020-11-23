---
title: 內嵌內嵌命令 (推送) -Azure 資料總管
description: 本文說明內嵌內嵌命令 (push) 。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 27b3052bf86d20b31b86f4f8540b5aa37a415fa0
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324579"
---
# <a name="ingest-inline-command-push"></a>。內嵌內嵌命令 (push) 

此命令會在命令文字本身中「推送」內嵌內嵌的資料，以將資料內嵌至資料表。

> [!NOTE]
> 此命令用於手動臨機操作測試。
> 在生產環境中，建議您使用其他內嵌方法，更適合大量傳遞大量資料，例如 [從儲存體](./ingest-from-storage.md)內嵌。

**語法**

`.ingest``inline` `into` `table`*TableName* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ] `<|`*資料*

**引數**

* *TableName* 是要內嵌資料的資料表名稱。
  名稱永遠與內容中的資料庫相關。
  如果未提供架構對應物件，資料表架構就是將為數據假設的架構。

* *資料* 是要內嵌的資料內容。 除非內嵌屬性另有修改，否則會將此內容剖析為 CSV。
 
 > [!NOTE]
 > 不同于大部分的控制項命令和查詢，命令的 *資料* 部分文字不需要遵循語言的語法慣例。 例如，空白字元很重要，或者 `//` 組合不會被視為批註。

* *IngestionPropertyName*， *IngestionPropertyValue*：會影響內嵌進程的任意數目的內嵌 [屬性](../../../ingestion-properties.md) 。

**結果**

結果是一份資料表，其中包含與產生的資料分區數目一樣多的記錄， ( 「範圍」 ) 。
如果未產生任何資料分區，則會傳回具有空白 (零值) 範圍識別碼的單一記錄。

|名稱       |類型      |說明                                                               |
|-----------|----------|--------------------------------------------------------------------------|
|ExtentId   |`guid`    |命令所產生之資料分區的唯一識別碼|

**範例**

下列命令會將資料內嵌至資料表 (`Purchases`) 有兩個數據行， `SKU` (類型) ， (`string` `Quantity` 類型) `long` 。

```kusto
.ingest inline into table Purchases <|
Shoes,1000
Wide Shoes,50
"Coats, black",20
"Coats with ""quotes""",5
```

<!--
You can generate inline ingests commands using the Kusto.Data client library. 
Compression lets you embed new lines in quoted fields.
    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);
-->