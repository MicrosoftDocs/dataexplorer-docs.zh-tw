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
ms.openlocfilehash: c7f692739071496ce492d168c6036a2c2adac8fd
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329039"
---
# <a name="commands-and-queries-management"></a>命令和查詢管理

## <a name="show-commands-and-queries"></a>。顯示命令與查詢 

`.show`傳回 `commands-and-queries` 資料表，其中包含已達到最終狀態的系統管理命令和查詢。 這些命令和查詢可供使用30天。

命令輸出中顯示的資訊類似于[。顯示命令](commands.md)和[。顯示查詢](queries.md)，但基本上可讓您以簡單的方式聯結這兩個結果集。

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
| 使用者                     | 字串     |
| Application              | 字串     |
| 主體                | 字串     |
| ClientRequestProperties  | 動態    |
| TotalCpu                 | 時間範圍   |
| MemoryPeak               | long       |
| CacheStatistics          | 動態    |
| ScannedExtentsStatistics | 動態    |
| ResultSetStatistics      | 動態    |

> [!NOTE]
> 針對查詢，的值 `CommandType` 為 `Query` 。
