---
title: 表管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的表管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 7b7e4c5c7111354864aa939ece76be2ab0a8ac15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519562"
---
# <a name="tables-management"></a>表管理

本主題討論表和相關控制命令的生命週期。

選擇下表中的連結,瞭解有關連結的詳細資訊。

| 命令                                                                                                                 | 作業                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | 管理表顯示屬性 |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | 管理引入對應        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | 建立/修改/移除表       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | 列舉資料庫中的表  |
| `.ingest`,、、、(`.set``.append``.set-or-append`有關詳細資訊,請參閱[數據引入](./data-ingestion/index.md))。                                                                                                                                                                                      | 資料引入到表格     |

## <a name="crud-naming-conventions-for-tables"></a>表的 CRUD 命名慣例 
(請參閱上表中連結的部分中的詳細資訊。
 
| 命令語法                             | 語意                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | 如果存在該類型和名稱的實體,則返回該實體。 否則,創建實體。                          |
| `.create-merge entityType entityName...`   | 如果存在該類型和名稱的實體,則將現有實體與指定的實體合併。 否則,創建實體。 |
| `.alter entityType entityName ...`         | 如果該類型和名稱的實體不存在,則出錯。 否則,將其替換為指定的實體。            |
| `.alter-merge entityType entityName ...`   | 如果該類型和名稱的實體不存在,則出錯。 否則,將其與指定的實體合併。              |
| `.drop entityType entityName ...`          | 如果該類型和名稱的實體不存在,則出錯。 否則,放下它。                                         |
| `.drop entityType entityName ifexists ...` | 如果該類型和名稱的實體不存在,請返回。 否則,放下它。                                        |
 
> [!NOTE]
> "合併"是兩個實體的邏輯合併:
>
> * 如果為一個實體定義屬性,但另一個實體未定義,則該屬性在合併的實體中顯示其原始值。
> * 如果為兩個實體定義屬性,並且兩個實體具有相同的值,則該屬性在合併的實體中顯示一次該值。
> * 如果為兩個實體定義了屬性,但具有不同的值,則引發錯誤。