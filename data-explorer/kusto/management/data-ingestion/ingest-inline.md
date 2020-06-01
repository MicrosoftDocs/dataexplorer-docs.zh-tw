---
title: 內嵌內嵌命令（push）-Azure 資料總管
description: 本文說明嵌入內嵌命令（push）
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2ac3a9a414d31492917cfb1768ce7bb1d7d8abb1
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257921"
---
# <a name="ingest-inline-command-push"></a>內嵌式內嵌命令（push）

此命令會將內嵌內嵌的資料「推送」到命令文字本身，藉此將資料內嵌到資料表中。

> [!NOTE]
> 此命令用於手動進行臨機操作測試。
> 針對生產環境使用，建議您使用其他的內嵌方法，以更適合大量傳遞大量資料，例如[從儲存體](./ingest-from-storage.md)內嵌。

**語法**

`.ingest``inline` `into` `table`*TableName* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ] `<|`*資料*

**引數**

* *TableName*是要將資料內嵌到其中的資料表名稱。
  名稱一律相對於內容中的資料庫。
  如果未提供任何架構對應物件，則資料表架構為將會假設用於資料的架構。

* *資料*是要內嵌的資料內容。 除非內嵌屬性另有修改，否則會將此內容剖析為 CSV。
 
> [!NOTE]
> 不同于大部分的控制命令和查詢，命令的*資料*部分文字不一定要遵循語言的語法慣例。 例如，空白字元很重要，或不會將 `//` 組合視為批註。

* *IngestionPropertyName*， *IngestionPropertyValue*：會影響內嵌進程的任意數目的內嵌[屬性](../../../ingestion-properties.md)。

**結果**

命令的結果是一個資料表，其中包含與產生的資料分區（「範圍」）一樣多的記錄。
如果未產生任何資料分區，則會傳回具有空白（零值）範圍識別碼的單一記錄。

|名稱       |類型      |描述                                                 |
|-----------|----------|------------------------------------------------------------|
|ExtentId   |`guid`    |命令所產生之資料分區的唯一識別碼|

**範例**

下列命令會 `Purchases` 使用兩個數據行（ `SKU` 屬於類型 `string` ）和 `Quantity` （屬於類型），將資料內嵌到資料表（）中 `long` 。

```kusto
.ingest inline into table Purchases <|
Shoes,1000
Wide Shoes,50
"Coats, black",20
"Coats with ""quotes""",5
```

<!--
You can generate inline ingests commands using the Kusto.Data client library. 
(Note that compression does let you embed new lines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->
