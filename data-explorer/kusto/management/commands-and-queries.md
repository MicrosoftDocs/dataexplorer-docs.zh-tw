---
title: 命令和查詢管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的命令和查詢管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 222d04939560342bd849c15f249b1a8a582316a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321024"
---
# <a name="commands-and-queries-management"></a>命令和查詢管理

## <a name="show-commands-and-queries"></a>。顯示命令和查詢 

`.show`傳回 `commands-and-queries` 資料表，其中包含已達到最終狀態的系統管理員命令和查詢。 這些命令和查詢都有30天的時間。

命令輸出中顯示的資訊類似于[ `.show` 命令](commands.md)和[ `.show` 查詢](queries.md)，但基本上可讓您以簡單的方式聯結這兩個結果集。

**語法**

`.show` `commands-and-queries`
 
**輸出**
 
輸出架構如下所示：

| ColumnName               | ColumnType |
|--------------------------|------------|
| ClientActivityId         | 字串     |
| CommandType              | 字串     |
| Text                     | 字串     |
| 資料庫                 | 字串     |
| StartedOn                | Datetime   |
| LastUpdatedOn            | Datetime   |
| 持續時間                 | 時間範圍   |
| 州                    | 字串     |
| FailureReason            | 字串     |
| RootActivityId           | guid       |
| User                     | 字串     |
| 應用程式              | 字串     |
| 主體                | 字串     |
| ClientRequestProperties  | 動態    |
| TotalCpu                 | 時間範圍   |
| MemoryPeak               | long       |
| CacheStatistics          | 動態    |
| ScannedExtentsStatistics | 動態    |
| ResultSetStatistics      | 動態    |

> [!NOTE]
> 若為查詢，的值 `CommandType` 為 `Query` 。
