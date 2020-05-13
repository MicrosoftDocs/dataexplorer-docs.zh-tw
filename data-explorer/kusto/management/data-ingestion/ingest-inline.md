---
title: 內嵌式內嵌命令（push）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的內嵌內嵌命令（push）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2b1766b295fd348639d8d91c8308a3ed0a35a3dc
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373404"
---
# <a name="the-ingest-inline-command-push"></a>內嵌式內嵌命令（push）

此命令會藉由「推送」內嵌于命令文字本身的內嵌資料，將資料內嵌到資料表中。

> [!NOTE]
> 此命令的主要目的是為了手動進行臨機操作測試。
> 針對生產環境使用，建議使用其他的內嵌方法，以更適當地大量傳遞大量資料，例如[從儲存體](./ingest-from-storage.md)內嵌。

**語法**

`.ingest``inline` `into` `table`*TableName* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ] `<|`*資料*



**引數**

* *TableName*是要內嵌資料的資料表名稱。
  資料表名稱一律會相對於內容中的資料庫，而且如果未提供任何架構對應物件，其架構就是將會假設用於資料的架構。

* *資料*是要內嵌的資料內容。 除非內嵌屬性另有修改，否則會將此內容剖析為 CSV。
  請注意，與大部分的控制命令和查詢不同的是，命令的*資料*部分文字不一定要遵循語言的語法慣例（例如空白字元很重要、不會將 `//` 組合視為批註等）。

* *IngestionPropertyName*， *IngestionPropertyValue*：會影響內嵌進程的任意數目的內嵌[屬性](../../../ingestion-properties.md)。

**結果**

命令的結果是一個資料表，其中包含多筆記錄，如命令所產生的資料分區（「範圍」）。
如果未產生任何資料分區，則會傳回具有空白（零值）範圍識別碼的單一記錄。

|名稱       |類型      |描述                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |命令所產生之資料分區的唯一識別碼。|

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
It is possible to generate inline ingests commands using the Kusto.Data client library. (Note that compression does allow one to embed newlines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->