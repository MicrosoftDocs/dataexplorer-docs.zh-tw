---
title: .inge 內聯命令(推送) - Azure 數據資源管理器 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 .ingest 內聯命令(推送)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 47da2df6ff974afb5e91224ade695dc0863b6817
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521364"
---
# <a name="the-ingest-inline-command-push"></a>.ingest 內聯命令(推送)

此命令通過「推送」嵌入在命令文本本身中的數據來將數據引入到表中。

> [!NOTE]
> 此命令的主要目的是用於手動臨時測試目的。
> 對於生產用途,建議使用其他更適合批量交付大量數據(如[從存儲中引入)](./ingest-from-storage.md)的攝入方法。

**語法**

`.ingest``inline``into``table`表名 = 引入屬性名稱引入屬性值 = ...] `(` * * `with` `=` * * `,` * *`)`• `<|` *資料*



**引數**

* *表名稱*是要將數據引入到的表的名稱。
  表名稱始終與上下文中的資料庫相關,並且其架構是如果未提供架構映射物件,則將為數據假定的架構。

* *數據*是要攝化的數據內容。 除非引入屬性另有修改,否則此內容將解析為 CSV。
  請注意,與大多數控制命令和查詢不同,*命令 Data*部分的文本不必遵循語言的語法約定(例如,空格字`//`元很重要, 組合不被視為註釋等)。

* *引入屬性名稱*,*引入屬性值*:影響引入過程的任何數量的[引入屬性](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)。

**結果**

該命令的結果是具有與命令生成的數據分片("範圍")一樣多的記錄的表。
如果未生成數據分片,則返回單個記錄,並返回空(零值)範圍ID。

|名稱       |類型      |描述                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|放大縮小字型功能 放大縮小字型功能   |`guid`    |命令產生的數據分片的唯一標識符。|

**範例**

以下命令將資料引入`Purchases`具有兩`SKU`列`string`(類型`Quantity`)`long`和 (類型 ) 的表 ( ) 中。

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