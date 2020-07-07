---
title: 資料表管理-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料表管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 4de0e749ad47b8f2e3f2c0f26d5d18466efaff97
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967293"
---
# <a name="tables-management"></a>資料表管理

本主題討論資料表和相關聯之控制命令的生命週期，這些功能有助於探索、建立和改變數據表。

選取下表中的連結以取得其相關詳細資訊。

| 命令                                                                                                                 | 操作                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | 管理資料表顯示內容 |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | 管理內嵌對應        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | 建立/修改/捨棄資料表       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | 列舉資料庫中的資料表  |
| `.ingest`、 `.set` 、 `.append` 、 `.set-or-append` （如需詳細資訊，請參閱[資料](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands)內嵌））。                                                                                                                                                                                      | 資料內嵌到資料表中     |

## <a name="crud-naming-conventions-for-tables"></a>資料表的 CRUD 命名慣例 
（請參閱上述表格中連結的小節中的完整詳細資料。）
 
| 命令語法                             | 語意                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | 如果該類型和名稱的實體存在，則會傳回實體。 否則，請建立實體。                          |
| `.create-merge entityType entityName...`   | 如果該類型和名稱的實體存在，請將現有實體與指定的實體合併。 否則，請建立實體。 |
| `.alter entityType entityName ...`         | 如果該類型和名稱的實體不存在，則會發生錯誤。 否則，請將它取代為指定的實體。            |
| `.alter-merge entityType entityName ...`   | 如果該類型和名稱的實體不存在，則會發生錯誤。 否則，請將它與指定的實體合併。              |
| `.drop entityType entityName ...`          | 如果該類型和名稱的實體不存在，則會發生錯誤。 否則，請將它卸載。                                         |
| `.drop entityType entityName ifexists ...` | 如果該類型和名稱的實體不存在，則傳回。 否則，請將它卸載。                                        |
 
> [!NOTE]
> 「合併」是兩個實體的邏輯合併：
>
> * 如果屬性是針對某個實體定義，而不是針對另一個，則會在合併的實體中顯示其原始值。
> * 如果同時為這兩個實體定義了屬性，而且兩者中有相同的值，就會在合併的實體中出現該值一次。
> * 如果同時為這兩個實體定義了屬性，但有不同的值，就會引發錯誤。